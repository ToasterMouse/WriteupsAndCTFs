![agentsudo_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_01.png)

**Disclaimer:** This walk-through contains spoilers and doesn't censor the answers. 

---

An easy but great machine and well worth the time to go through. It's semi-guided, meaning, there are questions that we must answer and these will guide our focus from task to task. 

---

### How many open ports are there?

In order to discover what ports are open, we'll use `nmap`. First, we'll do a basic scan and then a more in-depth scan on the ports we discover. Although this isn't an ideal way to do a scan, it's fine for this machine and it'll be quicker.

![agentsudo_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_02.png)

There are  `3` ports open. FTP on port `21`, SSH on port `22` and HTTP on port `80`.

---

### How do you redirect yourself to a secret page?

Let's check the website on port `80`. First, we'll open `/etc/hosts` and set the IP to `agentsudo.thm` and access the site via `http://agentsudo.thm` instead of the `MACHINE_IP`.

![agentsudo_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_03.png)

We will find that we need to supply a `codename` to the `User-Agent`. But which `codename` do we use?

---

### What is the agent name?

We just learned that we have to pass the **codename** for the user to the website via the `User-Agent` which will give us access to a secret page. We can open up `burpsuite` and send a bunch of **codenames** to the website via the `intruder` tab. We will be able to see successful attempts based on the response we get.

What **codenames** should be use? From the above screenshot we can see that the names seem to be just letters (like, `Agent R`). So let's proxy our request and send the alphabet.

Intercept.

![agentsudo_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_04.png)


Right click=>Send to Intruder (Ctrl+I).

![agentsudo_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_05.png)

When on `intruder` (on the `Positions` tab), change the `User-Agent` as above. Clear any selections, remove the `User-Agent` string and put in a value like `A`, highlight the character, then use `Add` and you'll get squiggly lines around the character. This will be the placeholder for our payload.

Move to the `Payload` tab and create a payload with all the characters of the alphabet. Because the agent capitalizes their **codename**, we'll only add capital letters. If we get nothing we'll try using lowercase and even numbers if we need to!

![agentsudo_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_06.png)

Then select `Start Attack`.

Once the attack is complete, we'll see a readout that shows an entry that has a different length and response code.

![agentsudo_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_07.png)

The response for `C` is a `302` (redirect), so let's follow it in the browser.

We can do this by right clicking, selecting `show response in browser` and then taking the resulting URL and pasting it into your browser's address bar (remember to turn off interceptor).

![agentsudo_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_08.png)

Now we have a username: `Chris`.

---

Not so much a question but now we need to find some passwords through cracking and brute-forcing. 

### FTP password

We can attempt to brute-force the password for `FTP` using `hydra` with the `rockyou` word-list.

![agentsudo_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_09.png)

---

### Zip file password

Zip file? Well let's jump onto `FTP` since we just got the password. If we jump onto `FTP` we can see that there are three interesting files. We'll grab them all by setting `prompt off`, and using `mget *`.

![agentsudo_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_10.png)

Now we have three new files: `cute-alien.jpg`, `cutie.png` and `To_agentJ.txt`.

Reading the text file we get a hint that the files are hiding something.

![agentsudo_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_11.png)

We can confirm this by using a cool forensics tool called `binwalk` which will find and extract an interesting zip file from the `cutie.png` image. 

![agentsudo_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_12.png)

We can extract these with another cool tool: `foremost` (or use `binwalk -e`).

![agentsudo_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_13.png)

These will be in the new `output` directory.

![agentsudo_14.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_14.png)

Trying to open this file will result in a prompt asking us for a password... of course.

However, we can attempt to crack zip passwords with `john the ripper`, but, it requires a specific format. We can obtain this with `zip2john`. We'll do this and send the output to a new file called `zippass`.

![agentsudo_15.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_15.png)

Now, we'll crack it with `john the ripper`.

![agentsudo_16.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_16.png)

---

### Steg password

What's `steg`? It's short for `steganography`. The magic of hiding things inside other things such as images. But first, let's check out the zip file contents.

![agentsudo_17.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_17.png)

Apparently, that's another user. An alien!? 

Well they are talking about an image and interestingly we didn't find anything unusual about the `cute-alien.jpg` file. However, just because a few tools didn't work doesn't mean none will. We know we're looking at `steganography` as the method of hiding data, so let's try to use some `steg` tools to see what is in the file... but, we will need a password. 

Thankfully, there are tools to brute-force `steg` password's too. First we may come across a program called `stegcracker`, however, this is deprecated and informs us that a program called `stegseek` will work to crack files using `rockyou`  much faster!

We can find `stegseek` [here](https://github.com/RickdeJager/stegseek).

Download the release and install it following the instructions on the `git` and then we'll try to crack the `jpg` file and see if anything happens.

![agentsudo_18.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_18.png)

Boy Howdy! That's quick!

If you tried using `stegseeker` you'll notice a HUGE jump in speed.

Looks like it found a password AND a message also.

---

### Who is the other agent (in full name)?

Let's read the message. Looks like it's been renamed using the extension `.out`.

![agentsudo_19.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_19.png)

---

### SSH password

We get that from the output above too!

---

### What is the user flag?

We can now login with `james@MACHINE_IP` to check out the system! 

We'll find a `user.txt` file and an image called `Alien_autopsy.jpg` in the directory we are in.

![agentsudo_20.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_20.png)

The flag is easy to get. Just use `cat` on the `user.txt` file.

![agentsudo_21.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_21.png)

---

### What is the incident of the photo called?

We can pull down the image in a few different ways... `scp` is the simplest

![agentsudo_22.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_22.png)

*Be careful to spell the filename correctly... it's misspelled. Copy and paste is ideal.*

Let's open the file with `eog` to see what it is.

![agentsudo_23.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_23.png)

![agentsudo_24.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_24.png)

*but...*

We can find out where this image comes from by using a reverse image search engine such as `tineye`.

Seeing as this `THM` room is a few years old we'll look at hits from `~2018/2019`. The image is from a `Fox News` article published on October 31, 2018.

![agentsudo_25.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_25.png)

![agentsudo_26.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_26.png)

---

### CVE number for escalation

This shouldn't be too difficult.

Let's start by enumerating the machine.

![agentsudo_27.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_27.png)

We can see that we are allowed to run `/bin/bash` as `ALL` but not as `root`. However, there is an issue with the way this is set as we actually can run as all. Why? `sudo`, depending if it's an older version (`<= 1.28`), won't check for the `UID` and therefore an attacker can specify the `UID` mathematically and force a command to be run as `root`.

Let's check our version:

![agentsudo_28.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_28.png)

This means we can use `sudo -u#-1 /bin/bash` to run `bash` and escalate our privileges. This works because `sudo` executes commands with root's `UID`. How? `-u#-1` will return `0` due to a bug in how this version of `sudo` resolved users with the `-u` switch. Sneaky!

We can discover the `CVE` with a simple `Google` search.

- `Sudo version 1.8.21p2 privilege escalation inurl:exploit-db`

![agentsudo_29.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_29.png)

We could even go direct to `exploit-db` and search `sudo` and sort by date, also.

![agentsudo_30.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_30.png)

Here we can find the `CVE` for this exploit by selecting it and checking the header information.

![agentsudo_31.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_31.png)

---

### What is the root flag?

![agentsudo_36.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_36.png)

The `exploit-db` page will have a similar explanation to the one I gave above about how this exploit works and will also provide a `POC` to try (above image). However, you don't need to use it and can simply run the command shown above.

Again, seeing as `sudo` won't check for the specified `UID` and an arbitrary one can be provided by the user, we can provide one using `-u#-1` (which will return `0`).

For clarity, this was discovered by `Joe Vennix` of Apple Information Security and works because specifying a `UID` of `-1` or `4294967295` or `${#FFFFFF}` will result in `0`. If the `sudoers` file contains an entry that includes a user being able to run a command as any user except root (`(ALL, !root)`), the system is affected ([Threat Post](https://threatpost.com/sudo-bug-root-access-linux/149169/), 2019).

A combination of the lack of `UID` check and overly permissive `sudoers` permissions, will lead us to grabbing a shell by just spawning the `/bin/bash` process using `sudo -u#-1`.  It's a sneaky little workaround as a normal call to `sudo /bin/bash` wouldn't work.

![agentsudo_32.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_32.png)

Now, let's exploit the machine:

![agentsudo_33.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_33.png)

Or:

![agentsudo_34.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_34.png)

After performing the exploit, we'll grab the root flag:

![agentsudo_35.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AgentSudo/images/agentsudo_35.png)

---

### (Bonus) Who is Agent R?

As above, so below???
