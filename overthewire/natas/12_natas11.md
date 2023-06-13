![natas11_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_01.png)

Another input, this time there is some hex data that you may be familiar with seeing in graphics editing software or if you've ever coded in `CSS`.

![natas11_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_02.png)

If we change the value and submit it we'll see that we're dealing with a form which we can use to change the background color.

![natas11_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_05.png)

Let's check the source code. This time there'll be more to parse.

![natas11_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_03.png)
![natas11_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_04.png)

We can see that there is a cookie that gets read and also that the level mentions that these are protected using XOR encryption. Also, when the `loadData` function gets called and the cookie is read, it uses `json_decode` after decrypting it. That means, the plain text version of this cookie will be in `json` format.

Importantly, in the cookie, if the value of the `showpassword` key is "yes", we'll get the password.

The `data` cookie can be found with the browser by traversing to: `DevTools -> Storage -> Cookies`. 

![natas11_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_06.png)

The source code shows us the form of the data it expects and because the password isn't appearing on the site we can conclude that the encrypted cookie in it's plain text form is a `json` representation of the `$defaultdata` array (below).

```php
$defaultdata = array( "showpassword"=>"no", "bgcolor"=>"#ffffff");
```

Cool! If we create our own encrypted version of the array, we'll be able to set the cookie through `DevTools` (or with `python`) and get the password for the next level. We will have to do a little reverse engineering but, to beat the level we can repurpose a lot of the site's own code.

Our goal will be to create an array (like below) and then encrypt it with XOR before finally encoding it in `base64`.
```json
{ "showpassword":"yes","bgcolor":"#ffffff" }
```

The source code includes the function it uses to encrypt the cookie.
```php
function xor_encrypt($in) {
    $key = '<censored>';
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
	    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}
```

The `key` is censored, so it may seem like we can't do anything, but this isn't a huge roadblock. Let's think a little bit about what XOR does.

---

# A little theory

If we have two of the values used in an XOR calculation, we can find the third by simply using XOR on the known values. If you've studied bitwise operators before you'd be familiar with XOR (Exclusive-OR). Without going into too much detail, an `OR` statement will return `true` if one or both operands are `true`. However, XOR (as it's name suggests) is exclusive. It will only return `true` if one of the operands is `true`. 

On a bit-level that means when two values are XOR'd together, only the bits that are exclusive are set for the resulting value.

```
0011 0110 = 54
1010 1100 = 172

	0011 0110
XOR	1010 1100
	_________
	1001 1010 = 154
```

Encrypting something would be no good if we couldn't decrypt it. Especially if it's a super secret message that must be relayed. If we take the above values as an example we can see how (when using XOR) they are related and allow for this two-way behavior.

We know that...
```
54 ^ 172 = 154
```

Say `172` is our key, `54` is our secret message and `154` is our encrypted message. Encrypting the message with the key predictably creates a cipher. How do we decrypt it? We simply use the `key` on the cipher, right?

```
154 ^ 172 = 54
```

Now we can successfully encrypt and decrypt messages. But, remember all these values are related. What if we XOR the secret message with the encrypted message?

```
54 ^ 154 = 172
```

We find the key! Now we can see the inherent weakness of using basic XOR for encryption and our path to victory. If we know the message and the ciphertext, we can generate the key and decrypt any subsequent encrypted messages.

```
plaintext ^ key = ciphertext
ciphertext ^ key = plaintext
plaintext ^ ciphertext = key
```

---

# Path to prevail

If we grab the plain text we found above: `"showpassword"=>"no", "bgcolor"=>"#ffffff"`. Then grab the cookie value we found :`MGw7JCQ5OC04PT8jOSpqdmkgJ25nbCorKCEkIzlscm5oKC4qLSgubjY=`. We can XOR these together and find the key.

If we check the source we'll find the way the site does it.

```php
$tempdata = json_decode(xor_encrypt(base64_decode($_COOKIE["data"])), true);
```

Note that it decodes the cookie with `base64` before sending it to `xor_encrypt`. Understanding this we can write our own `xor_decrypt` function to find the key, manipulate the cookie and encrypt it again. Also, note that it uses `json_decode` so when we start we want to `json_encode` the cookie's plain text.

Here's a start.

![natas11_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_07.png)

First, we create our own `xor_decrypt` function, it follows the same formula, just XOR one value by the other. 

What may be confusing is this line:

```php
$key .= $cookie[$i] ^ $plain[$i % strlen($plain)];
```

---

# A little more theory (sorry)

As you know `i` is the iterator variable and on each iteration it will be incremented. However, `$plain[$i % strlen($plain)]` looks odd. Specifically, `$i % strlen($plain)`.

Here, we are using the modulo operator to clamp the value returned to the length of the key. For example, `4`. In effect this will make sure that when performing the XOR calculation the key is repeated, like this:

```
key:     TESTTESTTESTTESTTESTTESTTESTTESTTESTTESTTESTTEST
message: supersecretmessagethatnooneshouldknowbutyouandme
```

Modulo is also known as the `remainder` operator. People will refer to it as one or the other based on preference. However, it works by returning the number of values left over after division. 

For example:
```
5 % 2 = 1
7 % 2 = 1
5 % 5 = 0
7 % 5 = 2
```

Two goes into five two times and there is 1 value left over. Five goes into seven once and there are two values left over, and so on.

So, if we have an iterator and we have a max value we're clamping this to (`4`), the following operations are performed:

```
0 % 4 = 0
1 % 4 = 1
2 % 4 = 2
3 % 4 = 3
4 % 4 = 0 (4 => 4 once, 0 remaining)
5 % 4 = 1 (4 => 5 once, 1 remaining)
6 % 4 = 2 (4 => 6 once, 2 remaining)
7 % 4 = 3 (4 => 7 once, 3 remaining)
8 % 4 = 0 (4 => 8 twice, 0 remaining)
...
n % 4
```

Note that when the left operand is less than the right operand, it'll always return the left operand.

---

# Path to prevail 2.0

![natas11_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_07.png)

To find the key we set our `$cookie`, making sure to `base64_decode` it. Then we `json_encode` the plain text array. Finally, we use the `xor_decrypt` function with the cookie and plain text array as arguments and echo the returned value. Here's what we get.

![natas11_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_08.png)

Note that the key seems to repeat after four characters. That's our true key. We can carve this out with the `substr` function.

```php
# in xor_decrypt
return substr($key,0,4);
```

![natas11_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_09.png)

Nice! Now we can create another version of the plain text array, this time replacing `no` with `yes`. We will copy the `xor_encrypt` function from the website and use it to encrypt the payload.

Here is the entire script.

![natas11_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_10.png)

We create two functions: `xor_encrypt` and `xor_decrypt`. They both do the same thing but work on separate parts of the XOR calculation. `xor_decrypt` is to discover the key and `xor_encrypt` is to generate our cookie payload.

To use these we first need to `base64_decode` the cookie we found (the one that is generated by default). Then we want to create the exact array that was used in the source code. Then we use our `xor_decrypt` function to find the key.

Then, we encode our new cookie payload (changed the value `no` to `yes`) and use it and the key with `xor_encrypt`. Finally, we `base64_encode` the output and print the results to `stdout`. We could also return the data using `base64_encode`, like this:

![natas11_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_12.png)

We'd simple have to change the `echo` call at the end to make sure everything works correctly, like this:

![natas11_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_13.png)

When we run our script we'll get the following as the output!

![natas11_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_11.png)

We can now replace the cookie in the browser and get the password!

![natas11_14.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_14.png)

---

# The Python Way

We can also use python to solve this level in one small script.

![natas11_15.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_15.png)

The only difference is we have to convert some of the characters to their decimal value before performing the XOR calculation. Then convert it back to it's character.

Running the script will return the password in just over a second!

![natas11_16.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas11_16.png)
