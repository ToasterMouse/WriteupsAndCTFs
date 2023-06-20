![natas26_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_01.png)

What is this!?

![natas26_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_02.png)

We have an app that will let us draw a line?

![natas26_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_03.png)

Success!

I played around with this for a bit and noticed that it also sets a `cookie`.

![natas26_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_04.png)

The cookie is called `drawing` and it is `base64` encoded. We may be getting a head of ourselves but let's see if we can decode it.

![natas26_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_05.png)

OH!?

That's serialized data. We can see that it is used to draw the line.

```
a:1:
{
	i:0;a:4:
	{
		s:2:"x1";
		s:1:"0";
		s:2:"y1";
		s:1:"0";
		s:2:"x2";
		s:3:"100";
		s:2:"y2";
		s:3:"100";
	}
}
```

I cleaned it up a little so it's clearer what it's referencing.

We can see that we first have an array of `1` and then an array of `4`. This inner array contains strings of data (the number is their length). You can see that they related directly to the `x1`, `y1`, `x2` and `y2` inputs on the site!

Now we know what the app does and that it's state is kept in a cookie. Let's check the source code.

![natas26_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_09.png)

First, we may notice that the site does indeed grab the values from the cookie to display them. Also we can see that it creates an image file `img/natas26_<session_id>.png`.

![natas26_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_10.png)

We're slowly discovering how this app works under the hood.

Perhaps there is a way for us to take some of this data we've found, serialize it and send it to the app?

The `Logger` class looks interesting. We can see that it's dealing with logging session data and writing to a log file.

![natas26_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_06.png)

This is an interesting class because it potentially gives us a way to interact with the machine. The only way we can input data for the machine to understand is through the cookie and we need to serialize it. Can we create a Logger object and serialize it and send it to the server so it can understand it?

If so, we might be able to interact with files on the system. 

We can see that `$initMsg` and `exitMsg` and both written to a file on the server. If we could use this class to write our own file, we could make it write the new level's password.

Serialization is a way for transmitting and storing data. So, if we create an object from the `Logger` class and serialize it and send it to the server it should be able to understand our object. Meaning, it'll create the log file we specify and write any data we supply to that file. Also, if it at any point calls it's `destructor` we'll also be able to add more data to the `exitMsg` variable. At least, that's the idea.

We'll have to instantiate the object on our end. Let's load up `sublime text` and copy the `Logger` code.

Where do we want to place the file?

![natas26_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_11.png)

From the source code we know there is an `img` directory. So, let's put our newly created file there and see what it says.

![natas26_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_12.png)

OK. We grab the class code and change the messages for our test and remove the parameter for the `__construct` function (after testing a little, it'll just cause us trouble). After we've done that we instantiate a `logger` object, we print what it looks like when `serialized`, we print an empty line to make things easier to read and lastly, we `base64` encode the serialized array and print it so that we can add it as the `drawing` cookie's value.

![natas26_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_13.png)

After running the script we can what the serialized data looks like when it's `base64` encoded and just on it's own. Also, we can read the log file we created and note that it gets both `initMsg` and `exitMsg` variable's data.

Let's see if we can post it to the site. First, of course, post it using the `drawing` cookie.

![natas26_14.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_14.png)

Refresh the page and note we get an error about trying to use the `Logger` class.

![natas26_15.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_15.png)

Let's try to find the file.

![natas26_16.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_16.png)

Boom! Got it! But look, it's only reporting the message from the `destructor`.

Let's try to inject a `php` payload into the exit message and make the file a `php` file. That way it should create it on the server and give us the password.

![natas26_17.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_17.png)

Now, we've just done the usual by using a `php` payload to read the next level's password and put it into an easy to remember file name.

![natas26_18.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_18.png)

All looks good, let's follow the same method we used above and read the file once we've created it through the `drawing` cookie.

![natas26_19.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_19.png)

Winning!

Sure, we get the password printed to us twice, that's OK, we got what we came for.

---

# The Python Way

![natas26_20.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas26_20.png)
