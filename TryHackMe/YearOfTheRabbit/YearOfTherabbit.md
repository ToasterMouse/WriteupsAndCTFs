![[yotr_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_00.png)

Here we are going to start off with what is stated to be a "gentle start". Let's go!

---

Let's start with our `nmap` scans which will find `ftp`, `ssh` and `http` services on the machine.

![[yotr_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_01.png)

Let's go to `http` first and when we do that we'll find the basic `Apache` setup page. I couldn't find `robots.txt` or anything like that so we'll do a `gobuster` scan.

This will show a single directory called `assets`. 

![[yotr_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_02.png)

We'll head there to see what we can find.

![[yotr_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_03.png)

`RickRolled.mp4` is what you think it is and `style.css` has the `stylesheet` for the webpage with an interesting comment.

![[yotr_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_04.png)

Looks like there is a "secret" directory. If we go there we first need to disable `javascript` in order to prevent the site from redirecting us to `YouTube`. Based on what we just found, you can guess what video it redirects us to.

![[yotr_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_05.png)

We can do that through `Mozilla` using `about:config`. Click the symbol with the two horizontal arrows to switch the value from `true` to `false`.

![[yotr_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_06.png)

And when we do

![[yotr_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_07.png)

Great. Getting rolled. We're not exactly stuck. We know that the `URI` we visited redirected us to this page from a `.php` file. It also seems like this page must have some `javascript` that redirects to `YouTube`. Which it does.

![[yotr_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_08.png)

But, that's on THIS page which is `/sup3r_s3cret_fl4g/` (which is the same as `/sup3r_s3cret_fl4g/index.html`). It's different from the page we visited, which was `sup3r_s3cr3t_fl4g.php`.

That means that when we visit the `.php` page it must redirect to `/sup3r_s3cret_fl4g/index.html`. We can check that with `curl -L` but let's look at it through `burpsuite`.

![[yotr_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_09.png)

We can see the `Location` of redirects in the response to check how the page is communicating to the site and to where each redirect will resolve.

`burpsuite` will show that this site redirects twice.

![[yotr_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_10.png)

- `/sup3r_s3cr3t_fl4g.php --> /intermediary.php?hidden_directory=/WExYY2Cv-qU --> /sup3r_s3cret_fl4g`

Now we've found a new directory. Let's check it.

![[yotr_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_11.png)

Hot babe? I'm guessing (because of being rick rolled) this is going to be a pig roast of some kind? I see you, I won't be fooled again!

![[yotr_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_12.png)

Expectations subverted.

That's all I can find on the site. That means that there is possibly some steganography shenanigans to do with this image. So, we'll do some forensics on it. The first and most basic test is to just use `strings` on the file to see what that returns.

![[yotr_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_13.png)

Would you look at that! Looks like we now have an `ftp` user and a potential password. That means we should be able to brute-force `ftp` with `hydra` to see what password in the list is the correct one.

We'll copy all the passwords to a file called `ftppass` to use as a word-list for `hydra`.

![[yotr_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_14.png)

Now we can simply use `hydra` with `-l` for a single username, `-P` for a pass list and the endpoint can be specified with `ftp://`.

![[yotr_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_15.png)

Now we have a password, we want to check `ftp`. So, let's do that.

![[yotr_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_16.png)

We will find a file called `Eli's_Creds.txt` that we can grab it with `get`. Using `prompt off` just allows us to grab it without confirming whether we want to download the file or not.

These might be `ssh` credentials... but...

![[yotr_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_17.png)

Well....OK.

If you're unfamiliar this is a coding language called `BrainFuck`. We can decode it on `decode.fr`, [here](https://www.dcode.fr/brainfuck-language).

![[yotr_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_18.png)

New user and password: `eli:DSpDiM1wAEwid`. 

Now let's try to use these on `ssh`.

![[yotr_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_19.png)

And we're in. Nice. 

We get a `MotD` which tells us of a secret place on the machine. Interestingly, it's referred to as `s3cr3t`. Let's see if we can find something with that name.

![[yotr_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_20.png)

We do!

![[yotr_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_21.png)

When checking the location we'll find a hidden file called `.th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly!`. I guess that's it then. Game over, man. Game over.

As if! The permissions allow us to read it, so let's read it.

![[yotr_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_22.png)

A now we have `gwendoline's` password. Just so we don't miss anything, we'll do a little enumeration of the machine as `eli` but, we won't find anything else we can use right now.

Let's switch to `gwendoline`.

![[yotr_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_23.png)

We can find the `user.txt` file in `gwendoline's` home directory.

![[yotr_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_24.png)

But, we're not done yet. Let's enumerate and see what else we can find.

![[yotr_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_25.png)

Oh nice, we can see that we can use `sudo` on `vi`, which you may be familiar with as being something that will allow us to run a privileged shell. 

However, look closely... closerly.... closelyier.....

It says `(ALL, !root)`. That means we can't run `sudo` as the `root` user. However, we could run it as `sudo -u eli` if we wanted to. There is an issue here that is worth understanding. If you've read some of my other write-ups it may jump out at you straight away. That being, if `sudo` is a version less than `1.8.28` it's possible to switch users by computing the `UID` of another user, as these are not checked in these older versions. The `root` user will be `UID=0`. 

So, while it's not possible to run `sudo -u root`, it might be possible to run it as `root` if we do so by `UID`. You may also be aware that with this vulnerability that it's not possible to simply change user by using the `UID`.

![[yotr_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_26.png)<br />
![[yotr_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_27.png)

This means that we need to create a situation where the value that gets input is computed to equal `0` but is not `0` itself. 

[Here](https://www.exploit-db.com/exploits/47502) is Joe Vennix's POC with an explanation of the vulnerability. 

```
"Sudo doesn't check for the existence of the specified user id and executes the with arbitrary user id
with the sudo priv"
```

Let's see if our version of `sudo` is vulnerable.

![[yotr_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_28.png)

Yup, it is. There are a few ways we can compute the `UID` of the root user it this case.

- `-u#-1`
- `-u\#$((0xffffffff))`
- `-u${#FFFFFFF}`
- `-u${#ffffffff}`
- `-u#4294967295`

All of these will equal `0` or `4294967295`.

So, we should be able to run `vi` now with `sudo` privileges. From there it's a simple case of running a shell with `:!/bin/bash`.

The way `sudoers` is configured means we have to run `vi` with the `user.txt` file.

![[yotr_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_29.png)

Execute the payload:

![[yotr_30.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_30.png)

Now we own the machine!

We can grab the flags and we're done!

![[yotr_31.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/YearOfTheRabbit/images/yotr_31.png)
