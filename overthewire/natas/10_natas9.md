![natas9_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas9_01.png)

Again, we'll check the website and find an input field...

![natas9_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas9_02.png)

...and some source code for us to analyse.

![natas9_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas9_03.png)

```php
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    passthru("grep -i $key dictionary.txt");
}
?>
```

We can see that the `$key` variable is set to whatever we add to the input. If it's not empty then it uses the `passthru()` function to perform actions on the command line.

You can read more about the function [here](https://www.php.net/manual/en/function.passthru.php). Basically, it executes whatever we tell it to on the command line - this is known as command injection. The `passthru()` function is running `grep` but our input is placed on the command line as the pattern to use when searching a file called `dictionary.txt`. 

We know that these machines are running `Linux` and so we can use `bash` syntax to perform some command injection. On the command line we don't HAVE to complete commands. We can stop previous commands using `;`, then supply our own command and then handle whatever comes after our command. In this case that'll be `dictionary.txt`.

The last thing we need to remember is that passwords for the next level are always accessible by our current user in `/etc/natas_webpass/<next_user>`.

That means we want to form a command like this: 

- `; cat /etc/natas_webpass/natas10;`

What happens here is simple, we end the `grep` call with `;` and then open the password file. We also add `;` afterwards to end our command cleanly.

![natas9_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas9_04.png)

Send the command to the input.

![natas9_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas9_05.png)

The password for `natas10` is returned to us! Nice!

---

# The Python Way

![natas9_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas9_06.png)

Once again `python` makes this attack very simple. All we have to do is point our script at the correct input (`needle`) and send the data using a `POST` request.

When the `data` is sent through to form it takes two arguments: `needle` and `submit`. The first, for the input payload, then second for the forms submit action.

We can identify these names in the source code.

![natas9_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas9_07.png)
