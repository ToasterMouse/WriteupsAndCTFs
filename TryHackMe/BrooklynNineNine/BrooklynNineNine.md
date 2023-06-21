![b99_00.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_00.png)

---

This is a very easy machine, especially seeing as I have a nice tool to help with probably the most difficult part of this machine. We'll get there. 

First we need to perform some `nmap` scans.

![[b99_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_01.png)

We can see three ports. Let's look a little deeper at them.

![[b99_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_02.png)

We can see that `ftp` allows `anonymous` access and we can also see an Apache web-server. These are the first ports of call for us. I would usually jump straight to `http` but seeing as there is `anonymous` access on `ftp` and an interesting file (`note_to_jake.txt`), let's go there first.

![[b99_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_03.png)

We'll log in and download the file using `get note_to_jake.txt`.

![[b99_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_04.png)

The note may not seem like it contains much but it's in fact very useful. It contains three potential usernames for the `ssh` service we discovered AND we now know that they are prone to using weak passwords. Therefore, there may be other security misconfigurations and/or weaknesses that lead to easily owning this box.

Now, let's check out the website.

![[b99_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_05.png)

It's pretty much a splash page for the show with a little note at the bottom about how responsive the website is.

After further enumeration of the site we will find that the it doesn't have a `robots.txt` file so, let's run a `gobuster` scan to see if we can uncover any directories.

![[b99_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_06.png)

Nope...Not a sausage.

![[b99_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_07.png)

*[The Young Ones - Flood](https://www.youtube.com/watch?v=YZp2S5owIgw)*

Let's check the source code.

![[b99_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_08.png)

Bingo! So, it seems that the image is hiding something.

Where is the image though? The source code reveals all.

![[b99_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_09.png)

Let's download it.

![[b99_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_10.png)

Now we can use that tool I was talking about to brute-force a `steg` password and rename the output correctly. It simply uses `stegseek` to perform the brute-force (it's super quick) with the `rockyou` wordlist. Then renaming any files correctly and displaying the password that locked the file. It doesn't do anything special, it just makes things quicker.

Here's what the tool looks like if you're interested.

![[b99_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_11.png)

Running the tool, we'll find the password and a hidden file called `note.txt`.

![[b99_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_12.png)

Now, let's read the file.

![[b99_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_13.png)

Cool, we found a password. I'm sure it's a nod to the show but I haven't seen it yet so please forgive me for not getting it.

Now we have a username and password, let's login over `ssh`

![[b99_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_14.png)

Nice! We're user `holt`. Let's do a little enumeration.

![[b99_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_15.png)

Two interesting files straight away net nothing for us.

![[b99_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_16.png)

The tried and true `sudo -l` on the other hand shows us something very interesting. 

User `holt` can run `/bin/nano` with root privileges without needing to enter a password.

`Nano` is one of those binaries we don't really want to see in the `sudoers` file, especially if a password isn't required to run it. Seeing one of these binaries always means we will have to check `GTFObins`. If you're not familiar with `GTFObins`, it's a repository of all binaries that can be used to bypass security on a `Linux` machine (the windows versions are called `LOLBas - (Living Off The Land Binaries, Scripts and Libraries)` and also have a repository)

>GTFOBins
>
>"The project collects legitimate functions of Unix binaries that can be abused to (~~get the f**k~~) break out restricted shells, escalate or maintain elevated privileges, transfer files, spawn bind and reverse shells, and facilitate the other post-exploitation tasks"

We can find many binaries on this repository, all with their own entries and sub-categories such as: `Sudo`, `SUID`, `Shell`, `Capabilities`, `File Upload`, `File Download`, `File Read/Write` and so on.

It's possible to search by sub-category or binary name. So, let's search for `nano` and see if it has an entry for `sudo`.

![[b99_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_17.png)

If we select the binary or it's sub-category, we'll see the approach we can take.

![[b99_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_18.png)

We can see that through `nano` we can enter some commands to make it spawn a shell for us which will escalate our privileges to root (we will get root because we're going to be running `nano` as the super user).

To spawn the shell we will use `reset; bash 1>&0 2>&0`. Here we are spawning a shell by calling `bash` from `nano` and redirecting `stdout` and `stderr` to `stdin` (this will give us pseudo-terminal functionality). For a full explanation on redirection, check this [article](https://www.brianstorti.com/understanding-shell-script-idiom-redirect/).

`1>&0 2>&0` basically means that we are redirecting both `stdout` and `stderr` to the file descriptor `stdin` (i.e. the same place as `stdin`). This is required for a new interactive shell process to start for us in the same terminal window.

If you're a unfamiliar or need a refresher: 

>stdin, stdout, stderr
>
>- 0: stdin
>- 1: stdout
>- 2: stderr

`reset` is a Linux command that will initialize a new terminal (technically, it's a symbolic link to `tset` (terminal set) binary.

![[b99_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_19.png)

More specifically, when `tset` is run as `reset` it:

![[b99_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_20.png)

Basically, it'll allow us to cleanly call our next command, which will be a new `bash` process.
Without `reset` you would still spawn a new `bash` process running as root, however, you won't be able to see what you're typing on the command-line, `stdout` though, will still show the result.

![[b99_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_21.png)

Lastly, you'll see above the line to spawn the shell two commands `^R^X`, this means `CTRL+R` and `CTRL+X`. These are simply hotkeys in `nano` for `Read File` and `Execute Command`.

Nice. Let's grab root.

First, we'll need to run `/bin/nano` as `sudo`.

![[b99_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_22.png)

Now we'll get the `nano` interface.

`Ctrl+R `-- This gets us to a sub-menu which will allow us to use the Execute Command function.

![[b99_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_23.png)

`Ctrl+X` -- This will give us a way to input commands.

![[b99_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_24.png)

Now , spawn a shell.

![[b99_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_25.png)

Win.

![[b99_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_26.png)

Now, to grab the flags. `curl` is on this machine so I'll use a simple `findflag` script I wrote to grab them for us. I'll serve it up on a python `http.server` and call it with `curl -s` while piping it to `bash` to invoke it.

![[b99_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_27.png)

And we're done.

---

# The Python Script

This is the script if you want to copy it:

![[b99_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BrooklynNineNine/images/b99_28.png)

*NF vs $NF... NF is the number of files found in the location (sanity check), $NF is the name and directory of files found*

It simply searches all directories for the flag files: `user.txt` and `root.txt` (some machines will name these differently, most will not). It then looks at if more than one of these files have been found (there are some machines that will place red herring flag files) and if it only finds one it echoes the content to the screen.
