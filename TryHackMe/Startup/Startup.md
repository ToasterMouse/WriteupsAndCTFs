 ![[startup_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_00.png)

# Welcome to Spice Hut!

```
We are Spice Hut, a new startup company that just made it big! We offer a variety of spices and club
sandwiches (in case you get hungry), but that is not why you are here. To be truthful, we aren't sure
if our developers know what they are doing and our security concerns are rising. We ask that you
perform a thorough penetration test and try to own root. Good luck!
```

---

We'll start by using the usual methodology for these easy machines by running two `nmap` scans.

![[startup_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_01.png)<br />
![[startup_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_02.png)

Checking the website is always a good place to start. Also, note the files in the FTP directory and how we can access it with anonymous access. We'll get to that!

![[startup_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_03.png)

Cool! Let's enumerate the site a little bit. We'll find that there isn't a `robots.txt` but, we'll find when we run a `gobuster` scan there exists a directory called `files`.

![[startup_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_04.png)

This is a directory that looks familiar:

![[startup_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_05.png)

It's the FTP directory!

The `notice.txt` file might be worth reading.

```
Whoever is leaving these damn Among Us memes in this share, it IS NOT FUNNY. People downloading documents
from our website will think we are a joke! Now I dont know who it is, but Maya is looking pretty sus.
```

And the image is exactly what the person is complaining about. There isn't anything to find here but, what's interesting is the fact that we can access the FTP directory over HTTP.

![[startup_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_06.png)

Let's check out FTP then. Because we can access it from the site, we should be able to upload a shell via FTP and execute it through our browser. So, we'll grab the `php-reverse-shell` script from `pentestmonkey`, change the IP, PORT and the shell to `bash`. Then save it as `shell.php`.

![[startup_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_07.png)

Now, set up a listener with `rlwrap nc -nlvp <PORT>` and go to the directory from a browser and click on the file we just uploaded.

![[startup_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_08.png)<br />
![[startup_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_09.png)

Nice!

Now we can enumerate a little bit. We'll find that we are not very privileged as `www-data` and that we can't even check out `/var/www`. 

If we look around the base directory with `ls -lah`, we'll see two interesting files!

![[startup_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_10.png)

Both are owned by `www-data` and so they stand out as odd because they're in the base system directory. Here we'll get the answer to our first question when we `cat recipe.txt`.

![[startup_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_11.png)

**Question:** `What is the secret spicy soup recipe?`<br />
**Answer:** `love`<br />

---

Now let's try to escalate our privileges. We want a better shell too! It might be easier to try to escalate laterally first. 

Let's check out this `incidents` directory. It contains a very interesting file `suspicious.pcapng`. This is a packet capture file that you can analyze with `wireshark`, `tshark`, `strings` or `cat`. It contains network packet information and being labelled `suspicious` may be interesting to us!

If we parse through the file we can find some information leakage. We'll notice a bunch of commands that were run on the system. But also, closer to the end of the file we'll see what looks like a user has tried to use a password with `sudo`.

![[startup_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_12.png)

It doesn't seem to be the password for `www-data` though. However, are there any other users on the system? We can check `/etc/passwd` or `/home` to find out.

![[startup_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_13.png)

So we have another user and what seems to be a password. Also, from our `nmap` scan we can see that `ssh` is available on the machine. Let's try to `ssh` as `lennie` using the password we found!

![[startup_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_14.png)

We'll spawn a bash shell because they are better and then we'll begin some enumeration. The typical easy wins don't net us anything. But, we can grab the `user.txt` file if we want. I usually keep that to the end.

Now, in the current directory we have two folders: `Documents` and `Scripts`.

![[startup_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_15.png)

`Documents` isn't interesting. However, `scripts` is.

![[startup_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_16.png)

We can see that the `planner.sh` script echoes some information to the `startup_list.txt` file and runs a file called `print.sh` from `/etc/`.

![[startup_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_17.png)

The permissions look interesting too. `planner.sh` is owned by root and `lennie` cannot write to the file, meaning that we can't manipulate it to escalate to `root`. But what about `/etc/print`?

![[startup_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_18.png)

Oh, interesting. This file is owned by `lennie` and we can `read, write and execute`!

This is a pretty big oversight, especially if `root` runs the `planner.sh` file. That means, because the `planner.sh` file executes `print.sh`, anything run through `print.sh` should maintain `root` privileges if run by `root`. This also means we can edit `print.sh` but cannot directly run `planner.sh`.

Next question we want to know the answer to is: is a `cronjob` running this file? 

Let's have a look.

![[startup_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_19.png)

Well, there doesn't seem to be any jobs. However, with `crontab` (which can be made specific to a user and is different from the global `/etc/crontab`) if `root` was running a `cronjob` we wouldn't necessarily see it here as the user `lennie`. 

How can we tell then? Well, `cron` will run a process and therefore if we check the process tree we should see the `cron` process running if there is one. We can do this with `ps auxf` which will list everything in a tree structure. This means we'll see the parent process and if we add `-A {N}` to a `grep` on the output for the keyword `cron`, we will see the relevant child processes.

![[startup_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_20.png)

Here we can see what I mean. We have used `grep` on `cron` listing 5 lines AFTER any hit and we'll see that `root` does indeed use `cron` on `planner.sh`. Nice.

That means we should be able to spawn a new shell as `root` using a `tcp` device. We can find these payloads from `PayloadAllTheThings`. Let's add the code to the `print.sh` file. 

First, we'll make sure to set up a listener using: `rlwrap nc -nlvp <PORT>`.

Now we'll edit `/etc/print.sh`:

![[startup_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_21.png)

We'll use `vim /etc/print.sh` to open the file in `vim` and edit it. Press `Insert` or `i` to enter INSERT mode and add the line to call a reverse shell to our machine using a `/dev/tcp` device.

When you've entered the line, press `ESC` then `:wq` and `ENTER` to save and quit.

Now, we'll wait for the shell to pop, it should take  ~1 minute on this box.

![[startup_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_22.png)

Win!

We can now confirm how the `cronjob` we found from the process tree works.

![[startup_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_23.png)

Now, we can go ahead and complete the room by grabbing the root flag. I wrote a basic shell script that will grab both `root` and `user` text files wherever they are.

![[startup_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Startup/images/startup_24.png)
