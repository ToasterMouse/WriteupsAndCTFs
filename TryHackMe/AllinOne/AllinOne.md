![[allinone_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_00.png)

This was a pretty cool machine. As the blurb for this machine says, there are many ways to exploit this box however, we'll look at one web method and only a few privilege escalation methods for this write-up.

---

We start with our `nmap` scans where we'll find the `ftp`, `ssh` and `http` services open.

![[allinone_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_01.png)

We'll find a Wordpress site at `http://allinone.thm/wordpress`. One of the first things we will do is run `wpscan` and let that go while we look around. We won't find too much (except a username: `elyana`) but, our `wpscan` will find a bunch (make sure to run it with an API token).

![[allinone_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_02.png)<br />*I ran brute-force just in case.*

`WPScan` finds a few `vulns` but the most interesting to me were the ones to do with plugins.

![[allinone_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_03.png)

If we check the `unauthenticated local file inclusion` exploit for `MailMasta`, we'll see how it works.

![[allinone_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_04.png)

Because of improper validation on an include statement we can grab files from the web-server.

If we use `count_of_send.php?pl=php://filter/convert.base64-encode/resource=/var/www/html/wordpress/wp-config.php`, we'll get the database configuration file. This will contain a password for the database that we may be able to reuse.

![[allinone_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_05.png)

We'll grab the encoded data and decode it (with `CyberChef`) and paste the file contents to sublime text so we can get some nice highlighting

![[allinone_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_06.png)

And now we have a password. But does it work for the `wordpress` dashboard?

![[allinone_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_07.png)

Yes the email is definitely correct...

![[allinone_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_08.png)

Yup! It works!

It's also possible to find the password using `gobuster`. The scan will uncover a file called `hackathons`. This file contains the content:

```
<html>                                                                                                                                       
<body>                                                                                                                                       

<h1>Damn how much I hate the smell of <i>Vinegar </i> :/ !!!  </h1>

<!-- Dvc W@iyur@123 -->
<!-- KeepGoing -->
```

That weird string is a substitution cipher. `Vinegar` is the hint for `Vigenere`. So, we can decode it with `boxentriq` using the key `KeepGoing`

![[allinone_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_09.png)

Nice. We have a few options we can go for to get onto the machine from here. The easiest is to abuse `Wordpress` themes. We'll upload a `php-reverse-shell` to the current theme's `404.php` file and trigger an error by going to a non-existent page.

First, however, we need to activate a different theme otherwise we won't be able to update the theme we want to change.

![[allinone_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_10.png)

Now, we'll add the payload.

![[allinone_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_11.png)

1. Select `TwentyTwenty`.
2. Select the `404.php` template file.
3. Copy in `php-reverse-shell`.
4. Update.

Now, switch back to the `TwentyTwenty` theme, go to the site and try to access a non-existent page (make sure to have a listener running).

![[allinone_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_12.png)

Here, `/a` doesn't exist.

![[allinone_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_13.png)

We're in but, we can't escalate to `elyana` straight away with the same password. We'll need to enumerate the machine.

---

## Escalation

When checking for `SUID` files...

![[allinone_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_14.png)

... there are three files that should really not have the `SUID` bit set.

`bash` is the easiest to work with as we can simply invoke it with `-p` to preserve privileges.

![[allinone_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_15.png)

If that's all you want you can grab the flags now and be done.

But wait, there's more!

---

## Escalation 2

We can also find interesting files that are owned by `elyana` or other users and find something interesting.

![[allinone_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_16.png)

This file contains `elyana's` password: `E@syR18ght`. From that we can escalate laterally and enumerate again. We'll then find that they can run `socat` as root with no `PASSWD`.

![[allinone_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_17.png)

---

## Escalation 3

If we check `crontab` we'll find a file being run as `root` every minute.

![[allinone_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_18.png)

If we check the file.

![[allinone_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_19.png)

We'll append a reverse shell to the file and wait for it to pop on a new listener and we'll get `root` again.

![[allinone_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_20.png)

---

## Escalation 4

When we checked for `SUIDs` we found `chmod` and, we can use that escalation too. With `chmod` we can do pretty much anything!

We could create a new password.

```
openssl passwd -6 -salt lolhacked itscookin
$6$lolhacked$eneLwiHwN1duMmnjkvS1EIWfRO.5IILdUFYRJP.fmrdDWsfRKNTX9AI6W85oWjamk9YEuilrx85AQXXPKoG5H/
```

Then, `chmod 777 /etc/shadow` and add our new password for the `root` user.

We could also `chmod 777 /usr/sbin/useradd` and use it to create a new "hidden" user. 

For example (as root user):

```
$ useradd -M -ou 0 -g 0 -s /bin/bash pwnd
```

![[allinone_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_21.png)

Generate a password

```
openssl passwd -6 -salt lol lol
```

![[allinone_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_22.png)

Add the password to `/etc/shadow`

![[allinone_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_23.png)

Now, we're basically a new `root` user. This will give us an escalation path. We can hide a `backdoor` on the web-server (spawning as `www-data`), or use `ssh`  (we'd have to set that up for this machine) and once we're in we can simply:

```
# su pwnd
root@elyana:/# whoami
root
```

![[allinone_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_24.png)<br />
*redundant `whoami`, lol*

Hopefully, our unique name can hide... at least if they don't check `/etc/passwd` or `/etc/shadow`. Using `-M` means we don't have a `home` directory so we are mostly "hidden".

We can even use the `elyana` user to escalate. As well as any `backdoor` that we may have.

![[allinone_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_25.png)

---

## With great power...

A backdoor may follow a process like:

![[allinone_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_26.png)<br />*place backdoor in a hidden directory on the website*

![[allinone_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_27.png)<br />*access it through the website*

![[allinone_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_28.png)

We can improve it by simply adding `-p` to the shell that's spawned in `php-reverse-shell`

![[allinone_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_29.png)

And now...

![[allinone_30.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_30.png)

It'll say `id` is `www-data` because of the other commands in the shell... however, `-p` will preserve the `root` privileges and we'll effectively be the `root` user.

We could also add our backdoor to `/tmp`, then `chmod 6777` the file and then use the `mail-masta` LFI vulnerability to invoke the shell.

![[allinone_33.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_33.png)

![[allinone_31.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_31.png)

![[allinone_32.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/AllinOne/images/allinone_32.png)

---

And we're done. We can grab the flags and use `base64 -d` to decode them.

```
┌─[parrot@parrot]─[~/Documents/THM/rooms/allinone/loot]
└──╼ $for line in $(cat flags.b64); do echo $line | base64 -d; echo ; done
THM{49jg666alb5e76shrusn49jg666alb5e76shrusn}
THM{uem2wigbuem2wigb68sn2j1ospi868sn2j1ospi8}
```

There are more escalation and attack vectors on this machine. These are just a few (as well as some proof of how devastating `chmod` with the `SUID` bit set can be). What else can you find?
