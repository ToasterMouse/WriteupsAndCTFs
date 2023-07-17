![[0day_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_00.png)

This box was a really fun one. Let's see if we can break into `0day's` website.

---

We'll start with an `nmap` scan as usual.

![[0day_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_01.png)

We have two ports open. Let's check out `http` first.

![[0day_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_02.png)

Cool website with sweet particle effects we can interact with. Nice!

Also, we get some discovery with a potential user: `ryan`.

Let's have a look around and see what else we can find.

![[0day_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_03.png)

There's quite a bit. However, we won't find anything on `/admin` (which was what first stood out to me).

`/secret` has a picture of a turtle. 

'Sup buddy.

![[0day_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_04.png)

However, `/backup` contains an encrypted private key! That's more like it.

![[0day_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_05.png)

We can easily crack it with `ssh2john` and find the password is `letmein`. However, it doesn't work against the machine. 

There is an interesting directory worth looking at: `/cgi-bin`. If we check out [HackTricks](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/cgi) we can find a whole article on `CGI`. Basically, it may be possible to do what's called a `shellshock` attack against a script on this endpoint. That would make sense. We have an image of a turtle after all!

[Here](https://www.exploit-db.com/docs/english/48112-the-shellshock-attack-%5Bpaper%5D.pdf?utm_source=dlvr.it&utm_medium=twitter) is a great explanation on how these attacks work. A quick and dirty explanation is:

```
This vulnerability in Bash allows remote code execution without confirmation.
A series of random characters, () { :; }; , confuses Bash because it doesn't
know what to do with them, so by default, it executes the code after it.
```

The "random" characters probably look familiar; they're a function declaration! But, `Bash` doesn't know how to deal with anonymous functions and so will then execute the code behind it.

So we know what we can do. But how can we do it?

Well we need a place to "talk" to bash.  We can attempt to discover these by checking the `/cgi-bin` directory for files with the `*.cgi` extension.

![[0day_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_06.png)

We'll find `test.cgi` in our `gobuster` scan. That means if we "talk" to this endpoint we should be able to mess with it.

![[0day_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_07.png)<br />*Say hello*

We can see that we get a response from this endpoint and so it might be possible to inject a `shellshock` payload into the `User-Agent`, `Cookie` or `Referer` headers to see what we can get back.

If we try things like `cat /etc/passwd`, `whoami`,`id`, etc. We won't get anything back because it's not returning the content to us. However, we can try a time-based attack to see if we'll get a delay in our response. We can do that with:

- `curl -H 'User-Agent: () { :; }; /bin/bash -c "sleep 5"' http://zeroday.thm/cgi-bin/test.cgi`
 
![[0day_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_08.png)

In the above example we'll notice about a 5 second delay to getting a response. Nice.

That means we could write a simple `python` script (or we could do it with `curl`) that will make a request with a reverse shell in one of the headers we specified.

![[0day_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_09.png)

Set up a listener and then run the script.

![[0day_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_10.png)

Now we'll do some enumeration. After a bit we'll run `linpeas` and find some kernel exploits that look like they could be useful to us!

![[0day_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_11.png)

We can grab the `dirtycow` exploit from [exploitdb](https://www.exploit-db.com/exploits/40616). It'll also show us how to build it.

![[0day_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_12.png)

Build it following the command in the script.

![[0day_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_13.png)

We'll ignore any warnings and now we'll serve the file up on using Python's `http.server` module.

Let's traverse to a place where we have read and write privileges (usually `/tmp`) and then download the file there.

![[0day_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_14.png)

Now we'll set the file to be executable...

![[0day_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_15.png)

...and execute the exploit to get `root`

![[0day_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_16.png)

Nice! Finally, we can grab the flags for the win!

![[0day_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_17.png)

After performing the exploit make sure to restore `/tmp/bak` to `/usr/bin/passwd`

```
mv /tmp/bak /usr/bin/passwd
```

## Some Interesting Stuff

The shell you gain after you've exploited the machine with `dirtycow` will be very unstable. 

Why is it so unstable?

A quote from [dirtycow](https://dirtycow.ninja/) and their [git](https://github.com/dirtycow/dirtycow.github.io/wiki/VulnerabilityDetails)
```
"A race condition was found in the way the Linux kernel's memory subsystem
handled the copy-on-write (COW) breakage of private read-only memory mappings.
An unprivileged local user could use this flaw to gain write access to
otherwise read-only memory mappings and thus increase their privileges on the
system."
```
Here, you can read more about [race conditions](https://en.wikipedia.org/wiki/Race_condition).

[LiveOverflow](https://www.youtube.com/watch?v=kEsshExn7aE) gives a great breakdown of the vulnerability (as he always does!). The exploit we used is slightly different in it's approach, however, it's the same principle.

So, as you can probably imagine, such a condition makes the resulting shell quite unstable. It'll also break the machine and it'll require a restart if you wait too long. If you can avoid using kernel exploits like `dirtycow`, do that. There are some versions of `dirtycow` that are more stable than others. However, this one. Not so much.

### There's is more than one way to root a machine

One of the more stable kernel exploits (not `dirtycow`) for this machine is the `overlayfs` exploit that we would have found when we ran `linpeas` or `linux exploit suggester`.

You can find it [here](https://www.exploit-db.com/exploits/37292) or if you're on `Kali`, through `searchsploit`.

Once we get it, we can build it with `gcc overlay.c -o overlay` and send it to the machine with `http.server` and `wget` as usual. Make it executable with `chmod +x` and run. But, we'll run into a problem.

![[0day_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_18.png)

We get a `gcc` error. The `linpeas` output will have told us that `gcc` is on the machine. We could also search for it.

![[0day_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_19.png)

What is `cc1` then? We can find a decent answer on [StackOverflow](https://stackoverflow.com/questions/11912878/gcc-error-gcc-error-trying-to-exec-cc1-execvp-no-such-file-or-directory)...
```
`cc1` is the internal command which takes preprocessed C-language files
and converts them to assembly. It's the actual part that compiles C. For
C++, there's cc1plus, and other internal commands for different languages.
```

... or Google.
```
cc1 is also referred to as "the compiler proper". cc1 preprocesses a c
translation unit and compiles it into assembly code.
```

Maybe we can look for that then? If we search for it with `find`, we will... find it.

![[0day_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_20.png)

Let's just add that to the `PATH` variable to get the system to look there.

![[0day_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_21.png)

Now let's run the exploit again.

![[0day_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_22.png)

We have a shell as the `root` user and it's much more stable! This time we don't have to be worried about the shell breaking when looking around the system and grabbing the flags.

![[0day_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0day/images/0day_23.png)

And we're done.
