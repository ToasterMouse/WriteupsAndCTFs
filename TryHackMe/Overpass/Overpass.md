![[overpass_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_00.png)

>What happens when a group of broke computer science students try to make a password manager? Obviously a _perfect_ commercial success!

This is a great box. I got a little tunnel vision when privilege escalating, so much so that the obvious point (that I had looked at a few times) was escaping me. Never felt so dumb during a light bulb moment. 

---

We'll start out by setting up `/etc/hosts` to make the `MACHINE_IP` point to `overpass.thm` which will make it a lot easier to navigate as constantly putting in IP addresses gets tedious!

Next, we'll perform an `nmap` scan. 

![[overpass_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_01.png)

![[overpass_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_02.png)

We have a `Golang` server on port `80` so let's check out the website first as brute-forcing `ssh` isn't what I want to be doing blind.

![[overpass_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_03.png)

A secure password manager, eh? Let's check the source code.

My eyes went straight to this.

![[overpass_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_04.png)

In case you're not familiar. When studying cryptography one of the first cipher's you learn about is called the `ROT13` cipher (a version of which `Julius Caesar` had used and so it's commonly referred to as a [Caesar](https://en.wikipedia.org/wiki/Caesar_cipher) [cipher](https://www.khanacademy.org/computing/computer-science/cryptography/crypt/v/caesar-cipher)). It's a type of substitution cipher which simply shifts characters in the alphabet by a specific number value. That means that these developers are using something that is very old and easily broken. All we'd need to do to break it is to shift every character in the alphabet used to the left by the value they used. For example, if they used `ROT13`, we'd shift the alphabet back 13 places to undo it.

That's some good information to keep in mind. 

Now, let's check out the downloads section.

![[overpass_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_05.png)

We'll see there are links to the source code, let's have a look at it to see if there's anything useful. Also, let's run a `gobuster` scan to see if there are any directories we can find.

In the source code we'll find the exact `ROT cipher` they used:

![[overpass_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_06.png)

Now we know that their password vault has very insecure encryption, if we find the vault we will be able to reverse the passwords with ease.

With our scan we'll find some directories that we've seen already but the most interesting one is called `/admin`

![[overpass_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_07.png)

Looking through the page's source code we'll notice that there exists some `Javascript` files, the most interesting being a login script

![[overpass_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_08.png)

These are always worth checking out as they will leak how logins work.

![[overpass_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_09.png)

This source code has a very interesting `login` function. A very interesting and insecure `login` function. Basically, if the returned status of the `response` to the server is "Incorrect credentials" then you can't log in. However, if it's not, then a cookie gets set with the value of the response and the window location goes to `/admin`. Does that mean we can set a cookie to anything we want? Well, yes actually!

![[overpass_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_10.png)

We'll set a new cookie called `SessionToken`, we'll give it any value we like and then refresh the page!

![[overpass_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_11.png)

Oh, dear.

Now we're on the back-end and it looks like we're straight up given an `SSH` key to log into the machine because, one of the developers is very forgetful. However, note that the key is encrypted. That's OK though because, we can crack that with `ssh2john` and `john`.

We'll copy the contents to a new file we'll call `idenc.key`. Now, we'll use `ssh2john` and send the output to a new file with `ssh2john idenc.key > hashtocrack`.

![[overpass_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_12.png)

Then, we'll run `john hashtocrack --wordlist=/usr/share/wordlist/rockyou.txt` and find the password for the encrypted file.

![[overpass_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_13.png)

We have the password so all we have to do is create a decrypted `id_rsa` using the password we just cracked. We can do that with `openssl rsa -i idenc.key`. We'll be prompted for a password and from there we'll copy the contents to a new file we'll call `id_rsa`. Then we'll change it's permissions with `chmod 600 id_rsa` so we can use it with `ssh`.

Here, I copied and pasted the contents to a file using sublime text after running `openssl rsa -i idenc.key`. Also, you can specify an `outfile` using `openssl rsa`, which you may have seen if you've read some of my other write-ups.

![[overpass_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_14.png)

![[overpass_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_15.png)

All we have to do now is use `ssh` to connect to the machine, making sure to supply the password if prompted.

Nice! We're in! We now begin the enumeration phase on our path to get root.

![[overpass_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_17.png)

We'll see a file called `todo.txt`. Apparently, the encryption isn't strong enough and that instead of using a sticky note to remember his password, `james` will use their `overpass` software.

Looking through his files we'll see a file called `.overpass`. If we open this files we'll see the encrypted text.

![[overpass_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_18.png)

The software was used to encrypt this password. Therefore, it must exist somewhere on the machine and we can use it to decrypt the password. If we look around we'll find the source code for `overpass` (`overpass.go`) and if we look through it we will see this:

![[overpass_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_19.png)

The software allows for the retrieval of passwords. We have a clear goal now so let's look for the `overpass` binary.

![[overpass_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_20.png)

Nice, that was easy.

![[overpass_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_21.png)

Finding `james'` password was even easier.

Unfortunately, it doesn't really help us. Using `sudo -l` to check their privileges won't get us anywhere. If we search for other users and try this password it doesn't work for them either (the only other normal user is `tryhackme`). So, it looks like we're stuck. Time for some more enumeration.

`cat /etc/crontab` will reveal something to us and we're now unstuck!

![[overpass_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_22.png)

There's a job being run every minute, it's running a script called `buildscript.sh` from `overpass.thm`... but that's here, right?

Yes, it is!

If we use `netstat -ano`, we don't find another machine running the website. Let's look for the site then... with no luck (`go` directory doesn't have it, neither does `/var/www`, etc.). 

The site must be running as a process.

![[overpass_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_16.png)

If we run `ps auxf` we will find that the site is being run from `tryhackme's` home directory, in a sub-directory called `server`. However, if we look around for leaked passwords or a way to laterally escalate to `tryhackme` we won't have any success. What can we do? Are we stuck again!?

Note that the `cronjob` is using `curl` to access `overpass.thm`. Well, that might mean that there is some internal `DNS` resolution. So, let's check `/etc/hosts`.

![[overpass_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_23.png)

Yes! localhost is mapped to `overpass.thm`. But, we can't access that directory to modify the `buildscript.sh` file.

If we go to `overpass.thm/downloads/src` we can download the script and check it out.

![[overpass_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_24.png)

It doesn't look like we can do anything to it, unfortunately. Roadblock after roadblock!

However, there is one thing we can do. Let's check the permissions on `/etc/hosts` .

![[overpass_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_25.png)

Everyone can read and write to the file. What if we created our own web-server on port `80` and served up a file called `buildscript.sh` in our own directory structure of `downloads/src`? Then all we would have to do is set `/etc/hosts` to point to our web-server and the `cronjob` should resolve to our version of the script instead of the intended version.

On our attack box let's make some new directories with `mkdir -p downloads/src`, traverse to `src`and add a script called `buildscript.sh`.

It'll be a simple one-liner that uses a `/dev/tcp` device to send a reverse shell back to our attacking machine.

![[overpass_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_26.png)

Right! Now we'll traverse back to the directory that contains the newly created `downloads` directory (this is because I want the web-servers "root" directory to contain `downloads/src` so the `curl` command resolves correctly). Then, we'll setup a web-server using Python's `http.server` module on port `80`. The `overpass-prod` machine runs their server on this default port as we saw in the above `ps auxf` screenshot.

![[overpass_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_27.png)

Now, we'll setup a listener using `rlwrap nc -nlvp 4142`. Make sure it's the port you specified in your `buildscript.sh` payload.

![[overpass_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_28.png)

Finally, we'll change `/etc/hosts` on the victim machine to read the IP of our web-server.

![[overpass_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_29.png)

Now, save the file and wait approximately 1 minute...

![[overpass_30.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_30.png)

...and a shell should spawn.

That's an interesting way to grab a shell! It's the same as serving up our own `http.server` to grab a file, but, using the victim's own local `DNS` resolution to grab the file for us.

Now we can grab the flags. I have my own little flag grabbing script that I use to pull down both the `user.txt` and `root.txt` files.

![[overpass_31.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Overpass/images/overpass_31.png)

And we're done!
