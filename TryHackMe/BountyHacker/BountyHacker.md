![bountyhacker_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_01.png)

This was a simple but fun room based around `Cowboy Bebop`.

>You were boasting on and on about your elite hacker skills in the bar and a few Bounty Hunters decided they'd take you up on claims! Prove your status is more than just a few glasses at the bar. I sense bell peppers & beef in your future!

---

These machines have a specific path that makes them very easy to follow. Let's follow the flow by answering the questions in order.

---

### Find open ports on the machine.

This is a simple action of running an `nmap` scan. To make this faster, we'll run two scans. We'll just search for all open ports and then only perform a more extensive scan on the ports discovered. It's not the ideal way, but, it's fine for most of the easy machines.

![bountyhacker_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_02.png)

We've discovered three open ports: `ftp`,`ssh` and `http`.  Running a more extensive scan, although only with default scripts, we find:

![bountyhacker_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_03.png)

More detailed information about each service, some discovery about the web-server (`Apache httpd 2.4.18`) and we've discovered a username to log in to the `ftp` service (`ftp`). 

---

### Who wrote the task list?

So, we need to find a task list, let's check the website and see if it's there.

![bountyhacker_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_04.png)

It's a very basic website and the source code is really bare bones. Let's run a few checks. We'll first look at `robots.txt` and find nothing. Then, we'll do a little directory busting:

aaaaaand...

![bountyhacker_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_05.png)

Nothing except a redirect to a directory called `images`, which...

![bountyhacker_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_06.png)

...nothing again.

Well, that's not all we can do. Remember `ftp` is loginable (Is that a word? It is now) with the username `ftp`. Let's check that out.

![bountyhacker_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_07.png)

If we list out the `ftp` server with `dir` we'll find two files.

![bountyhacker_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_08.png)

Nice! Looks like we've found the task list. But what's `locks.txt`?

We can download all files with `mget *`, or individually with `get locks.txt` and `get task.txt` and they'll be transferred to our machine. Now we can close `ftp`.

Let's open the files.

![bountyhacker_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_09.png)

The file called `task.txt` is relevant to our question, and there's the answer.

I'm curious about `locks.txt` too, let's open it:

![bountyhacker_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_10.png)

It looks like a list of passwords. That's cool! But where can we use them?

---

### What service can you brute-force with the text file(s) found?

There is only one other service we haven't looked at and we can brute-force. We can do so using `hydra`. The service is Secure Shell (SSH).

---

### What is the user's password?

By user, we'll assume they mean `lin`, it's the only name we really have that has any relation to the machine. Sure, the names on the website may be used, however, we'll keep them as something to fallback on if `lin` doesn't exist on the machine. 

In order to brute-force SSH, we'll use a tool called `hydra`. It's pretty simple to use and allows us to specify username/username lists and password/password lists, to brute-force a service. It can be used against SSH as well as multiple other services too.

We'll use 3 switches/flags here:

- `-l`: to specify the username of the account to brute-force.
- `-P`: to specify a newline separated list of passwords to try.
- `-v`: to provide a verbose output.

So, let's try to see if one of these passwords in the `locks.txt` file works.

![bountyhacker_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_11.png)

Nice! We found the password. 

---

### user.txt

We now have a username and a password: `lin:RedDr4gonSynd1cat3`. We've also got a service we know these work on: `SSH`. 

So, let's log in using the `ssh` command on the terminal.

![bountyhacker_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_12.png)

And we're in. We'll do a little basic enumeration first by using `ls`.

![bountyhacker_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_13.png)

It looks like the user file is right there. All too easy.

Let's open then file with `cat` and grab the flag:

![bountyhacker_14.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_14.png)

---

### root.txt

Now we need to escalate our privileges. Therefore we'll do more enumeration to see where and who we are and what machine we're on with the follow commands:

- `whoami`
- `id`
- `hostnamectl`

![bountyhacker_15.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_15.png)

Something worth looking for are misconfigurations of the machine environment. Sometimes, it's possible that an admin has given root privileges or `sudo` (super-user do) privileges to normal users that do not require them. The proper practice would be to follow the principle of least privilege. This principle is simply that a user should only have the level of privileges required for completing the task they're currently performing. That is, the privileges to work on specific data, resources and applications should only be available to this user for the current task and revoked as soon as the task is complete. 

Such an approach is useful for things such as preventing the spread of malware and decreasing vectors of privilege escalation during an attack, among others.

All this means that for an attacker, misconfigured privileges are an easy and quick win to `root`/`SYSTEM`. One way that we can check for `sudo` privileges on a Linux machine is to read the `sudoers` file. It can be found at `/etc/sudoers`. However, a user can check to see if they have certain custom privileges by simply using the `sudo -l` command and supplying their password (assuming the machine isn't so poorly configured as to not need a password for privileged actions like this).

Let's try it, we have a password so if our user has an entry in the file we should see something:

![bountyhacker_16.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_16.png)

There we go. We can see that the user `lin` is allowed to run `/bin/tar` as `(root)` on `bountyhacker`.  But, how do we use this?

There is a great little resource called `GTFObins`. This is a repository of all Linux binaries with discovered methods for privilege escalation and security bypasses. You can check the website [here](https://gtfobins.github.io/).

It provides an easy way to search for common binaries which can be refined with specific search terminology (searching for only binaries with `File Write` vectors, and so on). Let's do a basic search for `tar`.

![bountyhacker_17.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_17.png)

We can see at the bottom a link to the page for `tar` which describes all the methods listed to it's right. As you can see, the `tar` binary can allow us to grab a shell, perform file uploading and downloading, reading and writing to files, performing `SUID` actions and using `sudo`. Seeing as our user can run `tar` as root, we can simply follow the `sudo` example.

![bountyhacker_18.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_18.png)

We can see a command is provided to achieve privilege escalation. It will attempt to archive `/dev/null` (nothing) with the switches for `--checkpoint` and `--checkpoint-action`, which will use `exec` to call a new shell process. 

The checkpoint function displays progress messages every `Nth` record. A record is a chunk of memory that `tar` uses for writing content to an archive. 

Accompanying the `--checkpoint` switch is the `--checkpoint-action` switch which will run a certain action specified by the user when a checkpoint is reached. Here, we will specify that we want to run a new shell, thereby escalating our privileges as we will be running it as root! The shell will maintain it's privileges.

![bountyhacker_19.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_19.png)

And we're `root`! Now we can simply open up the `root.txt` file. These are typically always in the `/root` directory. However, if you're not convinced you can run `find / -name root.txt 2>/dev/null` to look for the file if you'd like. 

You can even mix the two with a little command substitution (or use the `-exec` switch for the `find` binary):

![bountyhacker_20.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BountyHacker/images/bountyhacker_20.png)
