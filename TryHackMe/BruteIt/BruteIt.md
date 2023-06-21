![[bruteit_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_00.png)

---

This is a very easy box but will teach some cool little tricks.

There are questions that can guide us through this box if we want to use them. Today, I decided to try using the `THM AttackBox VM`. So let's get started.

---

First, reconnaissance. We'll perform two `nmap` scans. This method isn't ideal, but, is OK when doing most of the easy `THM` machines as most (not all mind you) don't  filter ports. Plus, running two scans in this way is actually faster than performing one scan against all ports with service discovery and default scripts.

![[bruteit_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_01.png)

We will discover two ports open so let's do a more in-depth scan to discover the services associated with these ports.

![[bruteit_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_02.png)

From these scans alone we can go ahead and answer the Recon questions. 

#### How many ports are open?

`2`

#### What version of SSH is running?

`OpenSSH 7.6p1`

#### What version of Apache is running?

`2.4.29`

#### Which Linux distribution is running?

`Ubuntu`

---

#### What is the hidden directory?

If we want to find hidden directories we can do a quick `gobuster` scan.

![[bruteit_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_03.png)

From this we can see a new directory on the web-server: `/admin`.

---

#### What is the user:password of the admin panel?

Heading out to the site we'll find a default Apache page and by going to `/admin` we'll find the `admin` portal login page.

![[bruteit_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_04.png)

If we check the source code we'll see that there is a comment telling us that the username for the admin login is `admin`. 

![[bruteit_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_05.png)

Now, we'll want to brute-force the admin panel to see if we can guess the password. We can do this with `hydra` with the  `http-post-form` method.

Before that we'll just do a little test to see how the page responds and what the parameters it sends in the POST request are. We'll need to specify them in our `hydra` command.

![[bruteit_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_06.png)

We can see that the username and password are sent via a POST request as `user` and `pass`. Also notice the phrase `Username or password invalid` on the page after a failed login? We can use this phrase with `hydra` to ignore any incorrect password attempts.

To form the full command using `hydra` we'll specify a single name (`-l admin`), a password list (`-P /usr/share/wordlist/rockyou.txt`), the IP of the site, `http-post-form` then the endpoint we're making the request to with the relevant user and password parameters: `/admin/index.php:user=^USER^&pass=^PASS^:Username or password invalid`.

The last part may look a little confusing. All we are doing is specifying where the login page is located (`/admin/index.php`), the parameters with their placeholder variables (`user=^USER^&pass=^PASS^`)  and the string that we'll use to filter results. `^USER^` and `^PASS^` will be replaced with the current username/password being checked from the username/password lists. In this case `admin` will be checked against every password in the `rockyou` list.

Lastly, we'll specify 32 tasks (`-t 32`) to make it a little quicker.

![[bruteit_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_07.png)

We'll find the password `xavier` for `admin`.

---

#### Web flag

Now we'll log in and find the `web flag` and an `RSA` key.

![[bruteit_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_08.png)

---

#### What is John's RSA Private Key passphrase?

If we check the link to the key we'll get an encrypted private key.

![[bruteit_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_09.png)

The `THM AttackBox` doesn't have `ssh2john` which we need to attempt to crack the password for the key. So, we'll copy the contents to the file and upload it to an online `ssh2john` tool.

![[bruteit_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_10.png)

Once it's done, download the file and it'll contain the hash from `ssh2john`. I'll rename the file to `hash` and now we can use `john the ripper` to crack the hash.

![[bruteit_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_11.png)

---

#### user.txt

Now, we will use `openssl` to create a decrypted version of the private key file. It'll ask us to input the password, which we now know. 

![[bruteit_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_12.png)

Make sure to set the permissions correctly on the file using `chmod 600` and now we can `ssh` to the machine as the user `john`.

![[bruteit_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_13.png)

The `user.txt` file will be in the home directory for `john`.

![[bruteit_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_14.png)

---

#### What is the root's password?

For the last task we'll be doing some privilege escalation. After doing a little enumeration of the machine we'll end up listing out some permissions our user has from the `sudoers` file using `sudo -l`.

![[bruteit_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_15.png)

We can see that our user can use `cat` as `sudo`. That means we should be able to read ANY file on the system without supplying a password. 

One of the most important files on the system is the `/etc/shadow` file. It contains password hashes for users on the system. 

Read it with `sudo cat /etc/shadow`. 

![[bruteit_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_16.png)

Just next to the username, starting with `$6$`, is the user's password hash. We can now grab both `shadow` and `passwd` files and use the tool `unshadow` to crack the hash. We'll set the lines for the `root` hash from `passwd` and `shadow` to two files given the same names. Then we'll use `unshadow` and look at the result to see if it worked correctly. Then, we'll use `john` to crack the hash.

![[bruteit_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_17.png)

Now we'll have the password for the `root` user. We could also just use `cat` to read the `root.txt` file but, let's escalate our privileges to root using `su -` and entering the `root` password we just found.

![[bruteit_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_18.png)

![[bruteit_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/BruteIt/images/bruteit_19.png)

Just a quick note on `su`. We can switch users with this command, however, if we want to switch to another user with their environment variables (which will also place us in their home directory) we can supply `-`. So, in the above case `su -` will switch to the `root` user and place us in their home directory: `/root`. Say we did `su root`, we'd change to the `root`  user but our current directory will be the same place from where we performed the switch user command. If we did that in this case that'd be `john's` home directory.

And we're done!
