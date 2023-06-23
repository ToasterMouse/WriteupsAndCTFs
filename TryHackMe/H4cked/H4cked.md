![[h4cked_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_00.png)

For this room we'll look at a `PCAPNG` file to see how a hacker attacked a client's machine. Then we will jump back onto the machine in an attempt to recover it for them.

---

# Part 1

First we'll check the `pcapng` for what frames that exist. We can do this with `wireshark` and/or `tshark`.

![[h4cked_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_01.png)

In the output above, we can see FTP, HTTP and other frames of interest. Let's use a `wireshark` to make parsing the data a little cleaner (although, `tshark` is a great tool too).

We will use the questions in the room to help guide us.

First, we're asked to find the specific service that the attacker tried logged in to.

We know there is FTP in the packets so we'll check those packets by filtering on `ftp`.

![[h4cked_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_02.png)

We'll see that it looks like some brute-forcing was tried and was successful on the user and pass combination of `jenny:password123`

![[h4cked_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_03.png)

The first few questions are easily answered:

1. Service attacked = `ftp`.
2. Popular Van Hauser tool = `thc hydra` (we can find this by searching google for Van Hauser, also, it may pop out as obvious at first if you've used it before).
3. Specific username = `jenny`.
4. User's password = `password123`.

We can answer the next question by right-clicking on the `Login Successful` packet and selecting  `Follow -> TCP Stream`  and we'll get a cleaner readout compared to reading through each packet individually but, both will leak the `CWD`.

![[h4cked_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_04.png)

5. current FTP working directory = `/var/www/html`.

We are then asked about a backdoor file name, we'll see that the attacker used `STOR` over `ftp` which is the protocol command for uploading a file over [FTP](https://www.google.com/search?client=firefox-b-d&q=FTP+STOR) (other than `PUT`).

![[h4cked_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_05.png)

In the `wireshark` packet readout we can see the name of the file: `shell.php` when this request is made. 

When a file is transferred over `FTP`, the requisite `FTP-Data` stream will contain the content of the file. If we filter by `FTP-Data`, therefore, we'll find the file's content and see that it's using a `php-reverse-shell`.

![[h4cked_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_06.png)

In the code for this shell there are a bunch of comments which will help us answer the next few questions.

![[h4cked_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_07.png)

6. Uploaded backdoor name = `shell.php`.
7. Backdoor download URL = `http://pentestmonkey.net/tools/php-reverse-shell`.

Now we are asked to find what commands were used. We can do this by finding the `TCP` packets related to the execution of the shell through `http`. 

![[h4cked_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_08.png)

The shell was uploaded via `ftp` but would need to be execute from the web-page: `http://<DOMAIN_OR_IP>/shell.php`.

If we `Follow -> TCP Stream`, then cycle once through the stream...

![[h4cked_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_09.png)

...we can see what commands are being used. We can also see at the top of the output the `hostname` of the system.

![[h4cked_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_10.png)

8. Which command was executed after getting a shell = `whoami`.
9. Computer's hostname = `wir3`.

We'll be able to find the answers to the rest of the questions in here too.

![[h4cked_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_11.png)

10. Which command to spawn TTY shell = `python3 -c 'import pty; pty.spawn("/bin/bash")'`.
11. Which command to gain root = `sudo su`.
12. What was downloaded from GitHub = `Reptile`.

The hint to what type of backdoor Reptile is exists in a warning on the git, if you aren't exactly sure.

![[h4cked_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_12.png)

13. What type of stealthy backdoor is this = `rootkit`.

You can also tell this from the list of features, if you're familiar with other rootkits.

![[h4cked_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_13.png)

We're done with the analysis of the file. Now let's attack the machine to help the client start their recovery.

---

# Part 2

To begin the attack we'll want to follow the same vector. That means we'll jump over to `FTP` and then upload our own version of a `php-reverse-shell` and from there we'll execute it and become `root`.

Apparently, the attacker has changed the password. However, they may have chosen something easy to remember. This means it should be in the `rockyou` word list.

We'll use `hydra` to attack the machine but, first let's make sure we know what we're looking at by performing some `nmap` scans.

![[h4cked_14.PNG]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_14.PNG)

We only have to two services. Although this might not have been strictly necessary, being clear on what we're attacking is important.

Now, we'll use `hydra` to attack the `FTP` service. This is pretty simple. We'll attack the user `jenny` with the `rockyou` list over `ftp://MACHINE_IP`

![[h4cked_15.PNG]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_15.PNG)

A missed opportunity for the `jenny` meme, I am disappoint.

OK, let's jump onto the `FTP` service and upload our own version of `php-reverse-shell`. First, we'll grab the `php-reverse-shell` from the `pentestmonkey` git and then change the IP and PORT to our machine IP and any port we choose. We'll call our version `shell.php`, also.

![[h4cked_16.PNG]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_16.PNG)

Access `FTP`.

![[h4cked_17.PNG]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_17.PNG)

The other shell is still there, however, if we put our own one in, we'll overwrite it. We can do that with `put shell.php`.

![[h4cked_18.PNG]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_18.PNG)

Now, we'll set up a listener and execute the shell from `http://MACHINE_IP/shell.php`

![[h4cked_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_25.PNG)

![[h4cked_19.PNG]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_19.PNG)

Let's update our `TTY`.

![[h4cked_20.PNG]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_20.PNG)

Then, we'll switch user from `www-data` to `jenny` with the password we found. Thankfully, they're the same.

![[h4cked_21.PNG]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_21.PNG)

Now, we'll check our `sudoers` permissions to make sure the attacker hasn't changed anything.

![[h4cked_22.PNG]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_22.PNG)

We can see that `jenny` can run `(ALL : ALL) ALL` as root. That means using `sudo` we can run ANY command that we want. Nice. 

Follow the same vector as the attacker and simply escalate to `root` using `sudo su -`

![[h4cked_23.PNG]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_23.PNG)

The flag for this machine is in `/root/Reptile`. 

That's a win!

![[h4cked_24.PNG]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/H4cked/images/h4cked_24.PNG)

And, we're done.
