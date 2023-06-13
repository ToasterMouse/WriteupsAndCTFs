![natas10_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas10_01.png)

A new web site. Let's go!

![natas10_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas10_02.png)

Well, they now decided that they want to start filtering the input. We still get access to the source code so maybe we can see how they are trying to handle the input.

![natas10_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas10_03.png)

```php
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    if(preg_match('/[;|&]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i $key dictionary.txt");
    }
}
?>
```

Now we can see that a new line has been added to the code. It's filtering `;`, `|` and `&` characters. However, it's still using `grep`.

If we use `.` with a call to grep it'll read every character in any file we give it. We can form a payload like this:

- `. /etc/natas_webpass/natas11`

We could also use `.*` which will match all files and their contents in the current directory as well as any files we specify.

![natas10_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas10_04.png)

The site will output all the lines from each file. Specifically, `natas11` and `dictionary.txt`.

![natas10_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas10_05.png)

---

# The Python Way

![natas10_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas10_06.png)

We can grab the password with `python` using the same method for the last level. This time we make sure we format the payload without any `;`, `|` or `&` characters.
