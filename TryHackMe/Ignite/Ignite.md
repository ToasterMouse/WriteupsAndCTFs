![[ignite_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_00.png)

This was a pretty easy box to complete and it really shows just how vulnerable a machine can be. It deals, specifically, with misconfiguration of services and credential reuse. Just a reminder: Do NOT use the same password for multiple logins. Thank you for coming to my TED talk.

---

As always we will perform our `nmap` scans. Again, I will harp on this forever (until proven wrong at least) doing it this way is faster (for most easy/medium `CTFs/THM/HTB` machines). Just remember if you're really stuck to try running a proper `nmap` scan against all ports in case there are some filtered ports that would otherwise show up.

![[ignite_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_01.png)

We can see a single service appearing on the machine using our simple scan. The more verbose version that uses scripts shows us the service (`Fuel CMS` - you may have heard of it before) and it also uncovers `robots.txt` so we don't have to check it manually. It discovered 1 disallowed entry: `/fuel/`.

![[ignite_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_02.png)

First, let's head to the site to see what's up.

![[ignite_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_03.png)

We can see that it is the default page for `Fuel CMS` and it is `version 1.4`. Let's keep a note of that.

Let's head out to `/fuel/`.

![[ignite_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_04.png)

It's a happy little login page. 

We don't know the credentials but an easy win is to always look for default credentials. A lot of content management systems (`CMS`) will have default usernames and passwords to help during set up. However, they will usually always tell the owner to change these as soon as they log in. People can be very lazy and ignore these messages, however. So let's ask our pal Google to see if we can uncover default credentials.

We will find this page:

![[ignite_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_05.png)

Which will tell us...

![[ignite_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_06.png)

So, if the people setting `Fuel CMS` up haven't bothered to change this, we should be able to log in with these credentials.

![[ignite_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_07.png)

![[ignite_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_08.png)

Yup. Nice!

Now, let's look for a way to get onto the machine. Typically with a `CMS` (especially as `addmin`) we can expect there to be a way for us to upload files. What type of files? We can guess that `php` will be an extension that will work but, we don't know that `Fuel CMS` is coded in that. So, let's do a little research.

![[ignite_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_09.png)

We will discover that it indeed uses PHP, meaning we could potentially upload a `php-reverse-shell` script, find where they're stored and execute it. 

To find the shell code we want let's go to the [pentest monkey GitHub](https://github.com/pentestmonkey/php-reverse-shell). `kali` comes with one somewhere on the file system also.

I found three on my `kali` system in these places

![[ignite_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_10.png)

We'll try to upload it, however...

![[ignite_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_11.png)

Computer says `.php` is no.

The `Pages` section also results in an error.

![[ignite_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_12.png)

Well that sucks. But we're not done, not by a long shot. There is a database of exploits that might have something worth trying; called `exploitdb`. If we check it out we'll find that there are a few of these exploits and the one that I like the look of is this one.

![[ignite_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_13.png)

If we read the code we can see how we are supposed to use it.

![[ignite_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_14.png)

We can also see how it works. It sends it's payload to `/fuel/pages/select/?filter=`.

![[ignite_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_15.png)

That might be a little hard to read because it's URL Encoded. So, let's use CyberChef to decode it.

![[ignite_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_16.png)

OK, we can see that it's running `PHP` code to the `filter` argument. It's interesting how it works, basically it sets the `system` command to `$a`, then runs `$a('"quote(cmd)"'` to place quotes around the command to format it correctly. This is essentially the same as running `system("cmd")`.

The whole thing is run in a `while True` loop:

![[ignite_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_17.png)

So, we can see that it sets up a connection directly to the machine's command line so we can interact with it, basically, a pseudo-shell (web shell).

![[ignite_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_18.png)

We can also find the same exploit if we use `searchsploit`.

![[ignite_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_19.png)

Because it exists on the system we'll copy it over to our current folder instead of downloading it from `exploitdb`.

![[ignite_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_20.png)

Let's use the script to check to see what user we are and see if we can improve our shell. We want to try and improve our shell because when we look at the code snippets above we can see that it's not a full shell and so our interactivity will be minimal.

![[ignite_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_21.png)

Cool, we seem to be `www-data`, but, running `netcat` to a listener we have setup doesn't do anything. It seems that we can't get the system to call back to us. Most likely because we are using an exploit to talk to the system through a `while True` loop. 

Remember that we do have a `php-reverse-shell` that we tried to use before? We'll let's see if we can use `wget` to grab it from a `http.server` on our local machine.

![[ignite_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_22.png)

Success! It grabs the file but it'll keep making requests to our web-server... it's that dang `while True` loop again! 

We can assume if it keeps calling for the file, it has grabbed it at least once. So, we'll `CTRL+C` out of the exploit and try it again to check to see if the file is on the server.

![[ignite_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_23.png)

Yup! It's there. It's also in the website's base directory (we can see `index.php`, `robots.txt`, `assets`, `fuel`, etc.). That means that when we set up a listener to fire off the exploit, we'll just have to navigate to that location in our browser.

![[ignite_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_24.png)

Boom! 

![[ignite_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_25.png)

**(Hacker Voice)::** We're in.

Let's just upgrade our shell a little with Python's `pty` module. Now comes the next step. Enumeration!

We won't get anything with simple checks such as (but not limited to) `sudo -l` or `find / -perms /04000 2>/dev/null`.

But, a great place to look for stuff is in the web directory itself. Because default credentials were used to login it could be possible that credentials have been reused for `root` access or even left laying around on the server. These should be in configuration files which are always worth checking.

Researching a little about `Fuel CMS` we'll discover that it uses `SQL` and that there exists a useful schema file called `fuel_schema.sql`. There could potentially be credentials in there. So, we'll check the `/fuel` directory for it.

![[ignite_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_26.png)

![[ignite_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_27.png)

Bingo, we'll find a username and password stored in the `sql` file. It looks like it's probably the same one that we logged into the `CMS` with (this would stand to reason), however, let's leave no stone unturned and try to crack it. It looks like `SHA1` (`hashid` will confirm this), also it has a salt value.

If we check the `hashcat` examples page we'll find the mode number we need to crack it and also how we will format the hash: `pass:salt`.

![[ignite_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_28.png)

Let's set these to a file called `hash`.

![[ignite_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_29.png)

Now, we'll use `hashcat` to see if we can break it.

![[ignite_30.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_30.png)

![[ignite_31.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_31.png)

Yup. This one was very easy and it's the same as the one we logged in to the `CMS` portal with. This may have been obvious to some, but, I wanted to show it so if you were unaware that these will be the same, now you have proof. 

We can try using `su root` with the password `admin` on the machine but, that won't get us anywhere (no password reuse here). So we'll keep looking.

Let's look for some more configuration files.

Going through the machine we'll come across the `application/config` directory which contains three interesting files: `MY_config.php`, `MY_fuel.php` and `database.php`.

![[ignite_32.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_32.png)

Looking through the first two doesn't net us anything that we can use. However, when we open up `database.php` we'll find something really interesting.

![[ignite_33.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_33.png)

This file contains the database connection settings and we'll discover an array that shows us these settings near the bottom of the file and would you look at that, a `hostname`, `username` and `password`.

![[ignite_34.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_34.png)

We can see that the `hostname` is `localhost` (the machine hosting the database) and the `username` and `password` that are used to connect to it: `root:mememe`. `root` is the same name for the super user on Linux. What are the odds that we will have password reuse now?

![[ignite_35.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_35.png)

And there we are!

We can use `su root` and when prompted, put in the password we found to see that this machine has been very poorly configured.

Now we can just grab the flags and submit them for the win!

While looking around you would have listed out the `/home/www-data` directory and no doubt found that `user.txt` in this instance is called `flag.txt`. However, `root.txt` is named the same.

To finish this box up, let's grab the flags and print them nicely to the screen.

![[ignite_36.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Ignite/images/ignite_36.png)

```
You can copy and paste the one-liner if you'd like:
echo -n "user: " && cat $(find / -name flag.txt 2>/dev/null) && echo -n "root: " && cat $(find / -name root.txt 2>/dev/null)
```

And we're done.

---

All this lab needed was a little patience and being thorough in enumeration, making sure that we check and reading all interesting files. A good takeaway would be to make sure to always try and understand the technology in use, where it is configured, what is necessary in configuring it and how it can be configured. 

Although we call these "easy wins" when talking about `CTFs`, such misconfigurations do exist in the wild and we shouldn't take it lightly. Someone with malicious intentions can do a lot of damage because of password reuse, something that the user may not think twice about.
