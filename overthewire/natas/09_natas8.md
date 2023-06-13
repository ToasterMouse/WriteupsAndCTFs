![natas8_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas8_01.png)

When we access the next level we will find an input field with some source code that we will want to analyse.

![natas8_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas8_02.png)

If we check out the source code we can see that, once again, it's nicely formatted for us.

![natas8_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas8_03.png)

```php
<?      
$encodedSecret = "3d3d516343746d4d6d6c315669563362";

function encodeSecret($secret) {
	return bin2hex(strrev(base64_encode($secret)));
}
if(array_key_exists("submit", $_POST)) {
	if(encodeSecret($_POST['secret']) == $encodedSecret) {
		print "Access granted. The password for natas9 is <censored>";       
	} else {
		print "Wrong secret";
	}
}
?>
```

It may seem confusing at first, however, all we really want to pay attention to are three things. I do recommend reading through all the code so you know what's going on but, for the sake of brevity we'll only focus on the most important parts in this write up.

First.
```php
$encodedSecret = "3d3d516343746d4d6d6c315669563362";
```

Second.
```php
return bin2hex(strrev(base64_encode($secret)));
```

Third.
```php
if(encodeSecret($_POST['secret']) == $encodedSecret)
```

We have an `encodedSecret`, we have the method that is used to encode a string and we can see that the input we make to the form (once encoded) must equal the `encodedSecret`.

---

### A little theory

The `encodedSecret` is in hexadecimal. This is a representation of data that appears in base-16. Base-10, we are all familiar with, it goes from 0 to 9 and after 9 a new place is added to the left that increments everytime the first number "turnsover" (wraps around from 9 to 0). Like this: `0 1 2 3 4 5 6 7 8 9 10 11 12 13 14...`, and so on. Base-16 is a similar concept but instead of 10 values there are `16` values. These are related to the numbers `0-15` where values from `10-15` are represented by `A-F`. It starts from zero and follows like so: `0 1 2 3 4 5 6 7 8 9 A B C D E F`. 

Therefore, a number like `75`, will be represented in hexadecimal as `4b`, sometimes you may see it like `0x4b`. `0x` denotes a hexadecimal value. In this case, the number on the right represents `0-15` whereas the number on the left represents `n*16`. So, if we wanted to quickly calculate `75` to hexadecimal we can do so like this:

```
(75 % 16) = 11
(75 - (75 % 16)) / 16 = 4

11 = b
4  = 4
```
It can be easier to think of it as each position being a multiple of a number

```
what is 0x1a2b3c in decimal?

1 = multiple of 1048576
a = multiple of 66536
2 = multiple of 4096
b = multiple of 256
3 = multiple of 16
c = multiple of 1

So,

(1 * 1048576) + (10 * 66536) + (2 * 4096) + (11 * 256) + (3 * 16) + (12 * 1) = 1725004
```

However, with computers we don't have to worry so much about that, we can convert decimal to hex with any online converter or using almost any existing programming language's math library.

Hexadecimal is just another way to represent data and is able to be converted to decimal (base-10), or binary (base-2), or octal (base-8) as well as other formats. It's also worth noting that these values, when in a certain range, are related to ASCII characters. Again, these values are just alternate representations of the same data.

We can find a simple conversion table from [asciitable](https://www.asciitable.com/).

![natas8_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas8_04.png)

Take decimal `42`, it can be represented as hexadecimal `2A`, octal `052`, HTML `&#42;` or the character `*`. That means, if a string of characters is represented in `hex`, as it is above, we can convert it to it's character representation, assuming it falls within the relevant values. Not all `hex` values will be represented by human-readable text.

---

### Forging a path forward

If we take the second important piece of code:
```php
return bin2hex(strrev(base64_encode($secret)));
```

We can see that the input is encoded using `base64 encode`, it then reverses it and sets the value to `hexadecimal` with `bin2hex`. That means we can reverse this process and get the plain text.

This will look a little confusing but I'll step through what I've done here.

```python
hex = "3d3d516343746d4d6d6c315669563362"
base64.b64decode(bytes.fromhex(hex).decode()[::-1]).decode()
```

We set the `encodedSecret` to our own hex variable and we use it in the next line. We use `bytes.fromhex()` to convert the `hex` to a bytes string that will contain human-readable text (if it is human-readable that is). We add the `decode()` method so we convert from a byte string to a normal string, then we reverse that string with some python  string cutting `[::-1]`. This gets decoded from `base64` and then decoded again as we'll want to use a normal string and not a byte string.

Here are all the steps performed through the python interpreter so you can see what happens at each step.

![natas8_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas8_05.png)

Nice! Now we have the secret let's input the value we found

![natas8_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas8_06.png)

Success!

![natas8_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas8_07.png)

---

# The Python Way

This is a simple way to perform the whole attack chain in a single python script.

![natas8_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas8_08.png)

