![[wgel_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_00.png)

This is a very simple machine but with a bit of twist. 

---

First and foremost we are going to run our `nmap` scans.

![[wgel_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_01.png)

We can see two services, `ssh` and `http`.

We'll start enumerating the site on `http` and we'll first see the default Apache page.

![[wgel_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_02.png)

We can run a `gobuster` scan against this page and find a new directory called `sitemap` which contains a website called `unapp`.

![[wgel_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_03.png)

From `/sitemap` if we run another `gobuster` scan we'll find a super interesting directory.

![[wgel_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_04.png)

`.ssh` should not be publicly accessible.

If we check the folder we'll find a private key file. We'll copy the contents to a file, save it as `id_rsa` and change it's permissions with `chmod 600` so we can use it over `ssh`.

![[wgel_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_05.png)

Now we just need a user to use it with. Looking around the website we find a few names; `dave`, `emily`, `adam`, `noah` and `dorothy`, but, none of these will work. 

We should do a source code check. 

If we do, we'll notice a name being mentioned on the default Apache page...

![[wgel_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_06.png)

OK, so `jessie` seems like a user to test against the `ssh` service.

![[wgel_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_07.png)

Nice.

Let's enumerate!

We'll find the tried and true `sudo -l` nets us something really interesting.

![[wgel_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_08.png)

The user `jessie` is allow to run `wget` as `root` with `NOPASSWD`. 

Checking `GTFObins` we can find an entry with a few options.

![[wgel_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_09.png)

The first thought I had here was "obviously, I want a shell to grab the flag".

![[wgel_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_10.png)

However, if we try this method we'll quickly find out that the `wget` version on the machine is too old and doesn't include the `--use-askpass` flag.

![[wgel_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_11.png)

We still have a few other options. Some more dangerous than others, such as `File Write`. 

![[wgel_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_12.png)

If we wanted to, we could write `root:*:0:0:root:/root:/bin/bash` to `/etc/passwd` to see if we can get it to allow access to `root` without needing to supply a password. However, through `wget` this will include a bunch of protocol information that will break `/etc/passwd` and the machine. We could also read `/etc/shadow` and grab `jessie's` password, crack it and then use the `(ALL : ALL) ALL` declaration in the `sudoers` file. However, I had no joy in cracking the password with basic word-lists.

We could also just upload the file or it's contents to a listener (upload server or `nc`).

![[wgel_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_13.png)

The simplest, however, would be to just read the root flag file, right?

We can do that with `sudo /usr/bin/wget -i /root/root.txt`. Again a snag, it can't find that file!

![[wgel_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_14.png)

Let's slow down just a bit. We'll first grab the `user.txt` file. But where is it?

![[wgel_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_15.png)

It's not in `jessie's` main directory. However, after looking around for a bit we'll find it named as `user_flag.txt` in the `Documents` folder.

![[wgel_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_16.png)

![[wgel_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_17.png)

Nice!

We can assume that the root flag file is named similarly to the `user_flag.txt` file. Let's try to grab it from `/root` first.

![[wgel_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_18.png)

Another, cleaner, way to do this is to set up a `nc` listener and then use `--post-file` to send the contents of the file to the listener.

![[wgel_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_19.png)

![[wgel_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/WgelCTF/images/wgel_20.png)

And we're done.
