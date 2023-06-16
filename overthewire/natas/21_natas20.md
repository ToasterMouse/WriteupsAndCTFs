![natas20_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_01.png)

Back for more!

![natas20_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_02.png)

The site we're faced with allows us to change our name but also notice that we have an error when the server tries to read the session data.

Let's test the input first to see what it tells us when we use it.

![natas20_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_06.png)

Still some errors in how the site implements sessions. Let's have a look at the source code to see if we can nail it down.

![natas20_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_03.png)

First off, we can see that in order for `print_credentials()` to give us the password, `admin` must exist in the `$_SESSION` super global and it must equal `1`.

Note that we can also send some commands to `debug` and it'll print a debug message to us. Let's try that to see what happens by appending `?debug=admin` to the `URL`.

![natas20_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_07.png)

OK, that's interesting. It's trying to read a session file, but it doesn't exist. Just below this it says it's writing the file.

![natas20_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_08.png)

So, can we read an existing session file?
Let's keep looking at the source code.

![natas20_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_04.png)

We can see that when a user gets added it hasn't seen before it'll write them to `$_SESSION` and we can read that through `Debug`. So, let's add a user: `panda`. Then, we'll use `debug` to see if we can read anything about that user.

![natas20_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_09.png)

If we do that we can see that this time we read a name key which returns the value `panda`. What if we try to add a user `admin`?

![natas20_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_10.png)

Same thing. Remember that if `admin` is equal to `1`...

![natas20_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_11.png)

We get the password. 

Can we create a user to trick the code? Let's find out.

When submitting a request, `$_SESSION` gets updated with the values of the request.

![natas20_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_13.png)

Also, note that `print_credentials()` gets called every time the code runs. That means if we can successfully inject a key/value pair of `admin => 1` to `$_SESSION` the password will be put to the screen without any further interaction required.

We've been talking about `$_SESSION` but, What exactly is `$_SESSION`?

![natas20_14.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_14.png)

It's an associative array (`key => value`) that contains session variables. In short, it's super important. We know that when we submit something from the form it gets set to the `key` called `name`. Perhaps we could escape the `name` key to supply another `key` somehow?

If we check the source again, we'll see something interesting.

![natas20_16.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_16.png)

Specifically, in this section:

```php
foreach(explode("\n", $data) as $line) {
	debug("Read [$line]");
	$parts = explode(" ", $line, 2);
	if($parts[0] != "") $_SESSION[$parts[0]] = $parts[1];
}
```

What does that do? It's the key to victory!

Starting with `explode()`.

![natas20_17.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_17.png)

It separates a string by some delimiter. In this case, it's a newline first and then a space the second time it's called.

The script then goes on to set the first part as a key and the second as a value in `$_SESSION`.

That means, `beep\nsound boop` should have returned as:

```php
name => beep # \n separates beep from "sound boop"
sound => boop # "sound boop" is separated by a space
```

Why didn't it do that? Let's check the request to see what actually gets sent.

![natas20_18.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_18.png)

Ah ha! It escapes our newline character. However, what if we try to send it via python? That way we bypass the input field and perhaps the newline escape?

![natas20_21.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_21.png)

Our script just connects to the level and then mimics what we did manually. We post our payload to `name` and try to set it to `blip` while creating a new key/value pair (`sound => blop`) by adding a newline character (making sure the new pair is separated by a space).

Here's what get's returned.

![natas20_19.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_19.png)

I'll clean up the output to make it easier to read.

![natas20_20.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_20.png)

![natas20_22.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_22.png)

*Holy mackerel!*

We did it!

Now, we know that if there is a key/value pair that reads `admin => 1`, we win. So, let's make a new payload and handle the output so we can grab the password.

![natas20_23.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_23.png)

We don't have to worry about accessing the `$_SESSION` variables through `debug` now. Remember that `print_credentials()` is always called and that this function will check to see if `admin => 1` is in the `$_SESSION` super global.

![natas20_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_11.png)

Our script will use `sessions` instead of `requests` to create and maintain a session (using `requests.post/get` won't work).

Lastly, we will use the third printed line, `Password: <censored></pre>`, to pull the password with `regex`. The script now will grab the password and neatly print for us!

![natas20_24.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas20_24.png)
