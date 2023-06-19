![natas25_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_01.png)

Edgy-boi is edgy.

![natas25_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_02.png)

The source code is fairly long. Let's see what we can find.

![natas25_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_03.png)

![natas25_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_04.png)

What stands out right away for me is that some of the code is checking for directory traversal in the `lang` parameter. But, it doesn't do a great job at filtering it.

![natas25_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_05.png)

*I had to change the colors. The white was burning my eyes out like that scene in Indiana Jones... you know the one.*

We can see that if it detects that someone is trying to change directories, it removes `../` with the `safeinclude` function but, only if it finds `../` in the path. Then logs that a directory traversal attempt took place. That means, there is a possible some `LFI` vulnerability here.

Why is that not good enough to stop directory traversal? Well if it's just deleting `../`, what if we use the payload: `..././`? It'll see `../` and remove it: `. ../ ./`. But have happens to the left overs? 

This happens:

```
..././        => finds ../
. ../ ./      => removes ../
../           => left over characters: ../
```

It only checks once. So we can keep the characters that would allow us to traverse if we trick the script in this way.

What else does the code say?

![natas25_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_06.png)

It won't allow us to grab the passwords directly. Dang it.

However, other files it seems to be OK with us grabbing. It'll `include` them on the page. Oh yeah, definitely an `LFI` vulnerability.

What else does it do?

![natas25_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_07.png)

It can list files and we can see that it creates logs based on the `session_id`. The `session_id` is likely in a `PHPSESSID` cookie, like before.

![natas25_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_08.png)

There it is.

Let's try some directory traversal with our new found traversal bypass trick.

We don't know exactly how the directory structure for the website is structured. However, typically a site like this (running on Linux) will have the site be in the `/var/www/html` directory. Sometimes, it'll be right there and other times it'll be inside another folder (typically the name of the site), something like `/var/www/html/natas25`. Because there are so many sites for these levels, we can assume that's the case here. But, we should test that assumption.

What file should we test it on? On Linux, a file that is always there and can typically always be read by anyone is the `/etc/passwd` file. It no longer contains passwords (that's `/etc/shadow`) but has kept it's name.

Therefore, let's test our traversal abilities with the following payload.

- `..././..././..././..././etc/passwd`

If we have the path right the `/etc/passwd` file will be included on the page.

We can attempt this through the `?lang=` parameter that gets set when changing the language for the page. We'll see in the source code this is where a request gets made and filtered through the `safeinclude` function. 

![natas25_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_09.png)

But, hold up a minute. Notice that there's a new directory there: `language`. Therefore, we need to modify our assumption a little. We assumed the website was `/var/www/html/natas25`. If `language` is in this directory and is where our entry point to the system is located, we'll need to traverse back one more directory to get to the base directory as now the structure would be: `/var/www/html/natas25/language`. Let's see if we're correct by using this payload:

- `..././..././..././..././..././etc/passwd`

![natas25_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_10.png)

![natas25_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_11.png)

Oh yeah!

OK, what can we do now? We know that there is a log for `natas25`. Let's see if we can read that log.

![natas25_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_12.png)

You may know what I am getting at here, but, let's just read the file first. It's in `/var/www/natas/natas25/logs` and the name will use the `session_id`. Let's grab that from the cookies.

The path should be:

- `/var/www/natas/natas25/logs/natas25_kgb6k17gdaqjj63s1rnfsaj9ge.log`

Seeing as we're in `natas25/language`, we can simply traverse back one folder with `..././` and search for the file that way. Therefore, our payload will be:

- `..././/logs/natas25_kgb6k17gdaqjj63s1rnfsaj9ge.log`

![natas25_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_13.png)

You can see that we can read the log for `natas25`. Why this can be dangerous is because of an attack called `log poisoning`.

Basically, if we can inject some `php` code into the log, when we read the log the code will get executed. We can do some pretty nasty stuff with a vulnerability like this. We could get a shell on the system. We won't do that here. However, we will inject code that will read the password file for the next level.

How do we do that?

You can see in the logs that the `User-Agent` (Mozilla/5.0... blah blah blah) is recorded. In fact, if we look back at the logging code:

![natas25_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_12.png)

We can see clearly that is the case.

The great thing about the `User-Agent` (for us), is that we can specify whatever we want with it. We can make a request where our `User-Agent` says something like: `LOLH4CK3DSCRUBLMAOLMAO!`. 

Or, we could add some `php`, like:

```php
<?php system("cat /etc/natas_webpass/natas26"); ?>
```

Then, after sending a request with that `User-Agent` we can read the log and the `php` will execute and fill in where the `User-Agent` would usually be with the password for `natas26`!

![natas25_14.gif](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_14.gif)

OK. Let's send it a request in the browser with a new `User-Agent` and let's do some banned directory traversal to force it to log the request.

![natas25_15.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_15.png)

That should be enough to write to the log!

Now, when we read the log again!

![natas25_16.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_16.png)

Bingo!

---

# The Python Way

Of course, we need to show how to do it in `python`.

![natas25_17.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas25_17.png)

Our preamble is the same as always. 

1. We set the `User-Agent` to be our payload.
2. We force a log with memes.
3. We make a `POST` request to grab the cookie value (for the log file name).
4. We create the payload to access the log file.
5. We send a `GET` request to read the file (along with some memes).
6. ???
7. We carve out the logs! - profit!
