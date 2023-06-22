![[gamingserver_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_00.png)

This is a fun and easy box with a great privilege escalation vector that can be attacked in two ways.

---

Let's begin with some `nmap` scans. If you've read any of my other write-ups you'll know my spiel on doing it this way for easy machines. If not, basically, it's quicker though not ideal.

![[gamingserver_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_01.png)

We will find two ports open, `22` and `80`. 

Let's head over to the website and have a look around. We will also perform a `gobuster` scan at the same time.

![[gamingserver_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_02.png)

Not sure I know what this website is about but it's definitely here. Let's check for `robots.txt`.

![[gamingserver_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_03.png)

We have something! Let's head over to `/uploads` and see what's hiding there.

![[gamingserver_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_04.png)

A list of possible passwords, a hacker's manifesto and an image of beaker from the Muppets, cleverly named ;)

Nothing is that important here. 

Let's check our `gobuster` scan.

![[gamingserver_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_05.png)

It looks like another directory called `secret`. What's there!?

![[gamingserver_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_06.png)

A private key!?

![[gamingserver_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_07.png)

But, it's encrypted. That's OK.

First, we'll copy it to a text file and call it `secretkey`, then we'll use `ssh2john` to send the hash to a file we'll call `hash`.

![[gamingserver_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_08.png)

Now we'll crack it with `john`

![[gamingserver_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_09.png)

We can either use this key to jump to the machine via `ssh` and use the password or we can create our own decrypted key. The latter would be nicer.

We'll use `openssl rsa` and specify the key we found as the `in` argument and then we'll create a new private key with the `out` argument. We'll be asked for a password to decrypt the key, thankfully we have that.

![[gamingserver_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_10.png)

Before we can use it we have to make sure once it's decrypted we set it's permissions correctly with `chmod 600`.

After that we'll use `ssh` to jump to the machine.

![[gamingserver_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_11.png)

And we're in.

Here we'll find the user flag right away.

![[gamingserver_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_12.png)

While doing some enumeration we'll discover that this user is a member of the `lxd` group. `LXD` is a container manager for Linux. Similar to docker containers these are a virtualization method for running isolated Linux file systems.

Being a part of this group means that we can create a privileged container and use it to become the root user.

There are [two ways](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/interesting-groups-linux-privesc/lxd-privilege-escalation) that we can achieve this. The first method will create a container but it will lack some useful Linux binaries. This can make enumeration a little difficult but, not impossible. Below I will show how to perform both methods.

---

# Method 1

This method requires us to generate two image files on our `AttackBox` in order to generate the container image that will allow us to mount the root file system.

There's is a little bit of setup we need to complete.

First, we'll grab the dependencies:

![[gamingserver_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_13.png)

Second, we'll grab `distrobuilder`

![[gamingserver_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_14.png)

Third, we need to `make distrobuilder`

![[gamingserver_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_15.png)

Fourth, when that's done, we'll create a place for the images (these need to be in the `$HOME` directory)

![[gamingserver_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_16.png)

Fifth, grab the `alpine.yaml` file

![[gamingserver_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_17.png)

Sixth, we'll build the two files we'll need to transfer to the victim machine

![[gamingserver_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_18.png)

Seventh, We'll set up a web-server with Python's `http.server` module to serve the files...

![[gamingserver_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_19.png)

...and pull them down to the machine.

![[gamingserver_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_20.png)

That's the files taken care of, now we need to set up the container and access it. From here we will be using the `lxc` command from the victim machine.

We'll use `import` to add these two new files to a new image called `alpine` and list it to make sure it's there.

![[gamingserver_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_21.png)

We'll then initialize this image as a container called `privesc`, set the `security.privileged` flag to `true` so we can act as the `root` user and then list the container to see that it exists (sanity check).

![[gamingserver_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_22.png)

Lastly, we'll configure the container by adding `host-root`, a `source` and the `path`. This means that we are going to be mounting the root file system (`/`) to the path `/mnt/path` in the container.  We'll also set `recursive` to `true` to set all the files in the filesystem to the container.

Then we'll run the container with a `/bin/sh` shell.

![[gamingserver_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_23.png)

We're finally in and now we can enumerate to find the `root.txt` flag. Because some binaries are missing (here, `find` is missing), it'll require a more manual method to find the flag. However, if we assume the flag is in `/root/root.txt` (the usual `THM` spot) on the main system, when it's mounted it should be `/mnt/root/root/root.txt`.

![[gamingserver_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_24.png)

*The find binary is missing!*

---

# Method 2

The second method is a lot easier to do and doesn't require making any files. We can grab the `alpine-builder` from `github` and use the supplied `*.tar.gz` file to perform the escalation.

![[gamingserver_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_25.png)

We don't need to generate a file for another architecture as the system we're on is `x86_64`. So, simply serve up the file and grab it.

![[gamingserver_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_26.png)

We'll import the image and then run it, skipping the `init` that `HackTricks` shows for this method as the `default` storage pool already exists on this machine.

![[gamingserver_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_27.png)

The setup here is similar to method 1. We import the image and set an alias. We then initialize the container from our image and set the security privileges. Then, we add a device to the container and set a `source` and `path` as we did in method 1 (add host's `/` to `/mnt/root` on the image).

![[gamingserver_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_28.png)

After we've done that, as shown above, we'll start the container and use `exec` to run the container interactively. You can see that we can use `find` this time and we'll get the root flag quickly.

![[gamingserver_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/GamingServer/images/gamingserver_29.png)

And we're done.
