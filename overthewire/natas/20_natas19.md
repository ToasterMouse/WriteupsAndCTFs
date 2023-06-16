![natas19_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas19_01.png)

Another login, this time it's been made a little more difficult to crack.

![natas19_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas19_02.png)

We'll try logging in to see what we're given.

![natas19_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas19_03.png)

Similar, indeed.

![natas19_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas19_04.png)

Let's check the `PHPSESSID` again. When we do we'll see that it's in `hexadecimal`.

![natas19_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas19_05.png)

Let's see if we can make sense of it.

We can use `python` to convert the `hex` to it's ASCII representation.

![natas19_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas19_06.png)

Cool, let's change the cookie and grab the next level's password.

We'll need to do that with python once again, this time it'll look more complicated than it is. All we'll do is change the number that is prefixed to the `-admin` part and encode it back to `hex`. Then we'll set the cookie. We can assume, seeing as there is no source code here, that it's the same as the previous level and that we'll be told we're `admin` when we log in. So, we'll use that to determine if we were successful. 

However, failing that we can attempt to find what's `not` in the text too. Above, we are told "You are logged in as a regular user", so we could use that with `not` if we want.

The script "prologue" is very familiar by now.

![natas19_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas19_07.png)

We will start by setting the part of the cookie we know is constant. Then we'll use list comprehension to convert each character to it's hexadecimal representation.

![natas19_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas19_08.png)

To do so we use the `hex()` function. It expects the ordinal (i.e., `a = 97`) representation of the character for it to convert the value. Also, note the string cutting: `[2:]`. We use this because once hex has done it's thing it'll prefix the returned valued with `0x`. The string cutting is just removing that prefix.

If you are unfamilar with list comprehension, it's simply a way we can loop through a list and perform some action on the variables.

For example, this:
```python
appended_name = "".join([hex(ord(c))[2:] for c in data_username])
```

Is the one-liner (list comprehension method) for this:

```python
for c in data_username:
	appended_name += hex(ord(c))[2:]
```

We use `"".join()` to join all the separate converted characters into a string.

![natas19_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas19_09.png)

Now, we'll loop through every number from `0 - 640`. For each number, we convert it to `hex` just as we did before but, we make sure to use the string representation of the value to get the correct hex value (`"0" = 0x30`).

Then we set the cookie to be the hexadecimal representation of each `id` and `-admin`. So, `0-admin` will be `302d61646d696e`. We update the user to what we're doing (sanity check) and then send off the requests one after the other.

Our conditional will check to see if the output we know exists is `not` in the content and then handle the response so we can print it to `stdout` in a nice and clean way.

Cool! Let's now run the script.

![natas19_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas19_10.png)
