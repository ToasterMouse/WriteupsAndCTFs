![natas16_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_01.png)

This time we have another search function with more filtering.

![natas16_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_02.png)

Let's check the source code

![natas16_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_03.png)

This time we can see that it filters these characters `;|&1\'"` as well as the back-tick. 

Also, note that the `$key` is now inside double-quotes.

After testing the input for a bit we might think there's nothing we can do. However, let's try to do some `bash-fu`. If we use `$()` to insert a command like `grep`, we might be able to discover the next level's password using a similar method to the previous level. That is, one character at a time. `$()` (command substitution) will execute the command and fill the space it's in with the result.

This method is partially blind, however. That means, we won't see the password being returned to us so we'll have to use something else to figure out if we're guessing the right password. It's partially blind because we will still have something rendered to the page that we can use to figure out if we're on the right track (it just won't be the password).

How can we find the password?

The first thing we want to do is find a word that only appears once in the `dictionary`. Why? Because we can use it to get our bearings without flooding the output. You'll see what I mean.

![natas16_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_04.png)

We'll use the word `prettying`.

Now our `grep` payload will be something like this:

- `prettying$(grep ^<characters_to_search> /etc/natas_webpass/natas17)`

The `regex` symbol `^` simply means "starts with". So, we will test the password in `natas17` and if it starts with the character we give it we'll get no output. Then we'll append more characters to the end just as we did in the last level, each time we get no output we build our test string until we get the full password.

`prettying` will only show up in the output if our second `grep` call doesn't find a match. If it finds a match, then the output will be blank. Using a word instead of not using one makes it so the output is cleaner and we can base our conclusion on whether we found a letter in the password by the fact that the output is empty. This works because of how nested `grep` commands return (or do not return) on a match.

This is the output we'll get if we don't correctly match the password with our `grep` command.

![natas16_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_05.png)

This is the output when we do.

![natas16_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_06.png)

To show this a little more clearly, let's head to the command line.

I have two test dictionaries, each with one word in them.

![natas16_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_07.png)

If we try the payload out, we can see that when we have the incorrect character, the word we searched for before the command substitution is returned. However, when we correctly guess a character nothing gets returned.

![natas16_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_08.png)

What is happening is we're appending characters to the search pattern (here, that's `temper`). For example: `grep -i temper$(grep ^a test_dict.txt) test_dict2.txt` includes `$(grep ^a test_dict.txt)` which if it doesn't find anything, won't return anything. Therefore `temper` is tested against `test_dict2.txt` and because it exists in `test_dict2.txt` it gets returned to us. However, `$(grep ^p test_dict.txt)` matches `pasta` and so `temper` actually becomes `temperpasta` (because we're using command substitution) and is used as the pattern against `test_dict2.txt`. That word doesn't exist in `test_dict2.txt` so the entire command doesn't return anything. Now you can see clearly why picking a word is useful and how we can use it for our attack to know if we've found characters in the password or not.

Also, note that command substitution will work inside double quotes.

![natas16_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_09.png)

Now we know what we want to do. Let's write another Python script.

We'll pretty much follow the exact same formula as last time.

The setup:

![natas16_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_10.png)

The engine:

![natas16_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_11.png)

Note that the connection is the same as before. Our payload uses an `f-string` so we can concatenate strings in a way that's clean and easy to read.

The difference here is that our first `if conditional` is checking to see if the keyword we chose `is not` in the content. If it's not then we know from our tests that the character we just tested is correct. Then we append the character we tested to the list of characters we've seen.

Once again, once the `chars_seen` list is 32 characters long, we'll have found the password, so we can print it to the screen!

Now we can run the script. It'll take a minute to complete, you deserve a break!

![natas16_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas16_12.png)

It takes ~5 minutes to complete and then you'll have the password!
