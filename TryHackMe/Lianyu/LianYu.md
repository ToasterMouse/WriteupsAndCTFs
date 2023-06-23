![[lianyu_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_00.png)

This was a great little room. It's fairly easy and has some fun steps to take to beat it.

---

As always we will start the machine and perform an `nmap` scan to discover any open ports and their services. 

![[lianyu_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_01.png)

We can see some ports of interest, particularly `ftp`, `ssh` and `http`. We also have a port for remote procedure calls and another unknown.

> What is RPC?
> 
>**RPC (Remote Procedure Call)** allows a program on one machine to call functions and/or methods on a remote machine. It is not a transport protocol. 

Now for a more in-depth scan.

![[lianyu_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_02.png)

I usually always head for `http` first and foremost. This depends on what `nmap` has discovered in the scan and seeing that `ftp` doesn't have any hits for `anonymous` access or something similar, we'll first head out to the web-page to see what we can find.


![[lianyu_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_03.png)

It's a web-page dedicated to the superhero show, Arrow. Let's look around to see if we can find anything interesting.

The basic checks didn't result in anything. So, let's try to bust some directories.

![[lianyu_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_04.png)

Nice! We found a directory called `island`.

![[lianyu_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_05.png)

Seems to be a code word here. But there isn't. Maybe checking the source code will reveal it?

![[lianyu_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_06.png)

It's easy to get stuck on what to do next, however , we can also follow the questions in the room to see what we should focus on. It can be really helpful when learning to try and not lean on these guiding questions all the time but, go back and answer them when you've owned the box. In this case I got a little stuck and we get a really useful hint for the question `What is the Web Directory you found?`. The hint being: `in numbers`.

Perhaps this is a nod to the show? Maybe they need some kind of code or something of that nature? We can have a stab in the dark and think about pass codes that are numbers and a common type of number like this is four digit combination lock. Also, in films it's not uncommon to see four digit combination locks "securing" top secret, super advanced technology.

So, let's run with these assumptions and create our own list of numbers from `0000 - 9999` to run against the web-server. This can be done with `bash` or `python` pretty easily.

```python
for i in range(0,10000):
	print(f"{i:04d}")
```

We can also do it on the command line and send them to a file called `numbers`:

![[lianyu_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_07.png)

If you're interested, this isn't the only way to format the range to achieve this result in python. Other methods include (each in a for loop):

- `print('{:04d}'.format(i))`
- `print("%04d" % i)`
- `print(format(i,'04d'))`

It can also be achieved in `bash` with:

```bash
for i in $(seq -f "%04g" 0 9999)
do
	echo $i
done
```

![[lianyu_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_08.png)

OK, now we know what we're doing and why, let's throw our new list and the new endpoint we found: `island`.

![[lianyu_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_09.png)

Great, we found the endpoint `2100`. Pretty simple. With just a little bit of programming knowledge we can easily find the correct directory.

Now we'll traverse the directory to find a page with a deleted YouTube video.

![[lianyu_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_10.png)

Let's check the source code again.

![[lianyu_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_11.png)

`.ticket`? That makes me think that what it's talking about is a file extension. We can "avail" ourselves of it. I'm guessing that means it's in the directory we're sitting inside. Therefore, we can directory bust one more time, but this time we'll search for extensions ending in `.ticket`.

Running this scan with the `/usr/share/wordlists/dirb/big.txt` word list won't give us a hit, so we'll use a bigger list from [SecLists](https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content). The name of this list is `directory-list-2.3-big.txt`.

![[lianyu_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_12.png)

Got it! We found a file called `green_arrow.ticket`. Let's check it out.

![[lianyu_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_13.png)

Interesting. However, if we now try to use this to log in to either `ftp` or `ssh`. It won't work. It certainly looks like it could be a decent password. 12 characters in length, numbers and characters (could use some symbols... wait). If it's not THE password. Is it the password but encoded?

It could be one of the `base` forms of encoding. Let's see what we can find by attempting to decode it as `base64` first (one of the most common encoding formats used in `CTFs`).

We'll find that it's not `base64` or `base32` but, `base58`.

![[lianyu_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_14.png)

That looks like a password. Let's try it on `ftp` to see what we can find.

![[lianyu_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_15.png)

Success. 

![[lianyu_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_16.png)

We will find three images. Let's grab them by using `binary` and `prompt off` and then `mget *` to grab the files. Checking the other files, specifically `.bash_history`, will tell us that we should check `.other_user` and when we do we'll get some history on `Slade Wilson` (Deathstroke). Nice.

The images are interesting and when we open them we can see that the file called `Leave_me_alone.png` is broken.

Doing a little analysis on the image we can see that it's being considered simply as `data`. So if we check a `hexdump` of the file we'll see that it is in fact a `PNG` as it contains the usual headers like `IDHR`, `IDAT` and `IEND`.

![[lianyu_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_17.png)

If you want to know more about `PNG` headers check out [libpng](http://www.libpng.org/pub/png/spec/1.2/PNG-Structure.html). There is also a very useful graphic made by `Ange Albertini`, below:

![[lianyu_18.jpeg]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_18.jpeg)

What we'll discover is that the header on the file we have is incorrect. Luckily, it's only in the first 6 bytes so we don't have to mess around with sizes and CRCs. All that's needed is the `PNG` magic number. For `PNG` the file signature (magic number) is: `89 50 4E 47 0D 0A 1A 0A`.

These are at the very start of the file and determine the file type. A huge list of them can be found [here](https://www.garykessler.net/library/file_sigs.html) (provided by Gary Kessler).

If we open up the file with `hexeditor -b` we can change the first 6 values to the correct ones.

![[lianyu_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_19.png)

![[lianyu_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_20.png)

We can now save and exit with `Ctrl+X` and check the file and open it with `eog`.

![[lianyu_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_21.png)

![[lianyu_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_22.png)

OK, so it looks like we have a new password... `password`. Secure.

But what can we do with it? Well, we have two other pictures and `steganograpy` is what immediately comes to mind for me. Is there anything hidden in the other files? They are `jpg` files and a lot of `steg` tools will deal ONLY with `jpgs` (it's promising).

There are two things we could have done here, we can use this password with `steghide` on either of the two other images, we could use it with `stegseek` or if we didn't know the password we could also have brute-forced this password with `stegseek` using the `rockyou` password list. Pretty cool!

We know the password so we can simply extract any potential data like this:

![[lianyu_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_23.png)

We will discover by trying the files one at a time that the file `aa.jpg` had a zip file hidden inside it!

Again, the same can be achieved with `stegseek` and the `rockyou` word list.

![[lianyu_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_24.png)

All we have to do for the `stegseek` version is to rename the file `aa.jpg.out` to the original file name (or any name, as long as it has the correct extension) given in the output. Here that is: `ss.zip`. 

You can automate this with `bash` or `python`.

Like so:

![[lianyu_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_25.png)

![[lianyu_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_26.png)

If you know it's going to be a zip file (or any other extension for that matter) then you could automate that too. For example:

![[lianyu_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_27.png)

![[lianyu_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_28.png)

Continuing; after using `unzip` on the file we'll find two new files: `passwd.txt` and `shado`.

![[lianyu_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_29.png)

Now we have a new password. Possibly for `ssh`?

Let's try!

It won't work for the user `vigilante`. However, is there another user? The `.other_user` information we found before mentions `Slade Wilson` and if we use `ls ../` on the `ftp` sever we'll be brought to the `/home` directory on the machine where we will discover the other user: `slade`.

![[lianyu_30.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_30.png)

The password will work when logging in as `slade`.

![[lianyu_31.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_31.png)

We want to escalate our privileges, so we'll do a bunch of the basic checks first. 

When using `sudo -l`, we'll find...

![[lianyu_32.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_32.png)

Looks like `slade` can run, with a password, `/usr/bin/pkexec` as `root`.

We can check for `pkexec` on `GTFObins` and find a single entry for `sudo`:

![[lianyu_33.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_33.png)

Let's try it.

![[lianyu_35.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_35.png)

Simple as that! Now we can grab the flags and complete the room.

![[lianyu_34.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Lianyu/images/lianyu_34.png)

A simple but really fun room!
