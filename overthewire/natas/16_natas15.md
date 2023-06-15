![natas15_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_01.png)

This time we only have a form field for a username.

![natas15_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_02.png)

As usual, let's check the source code.

![natas15_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_03.png)

This one is a little harder than the last. We have an input that seemingly will only check the username. Remember from the last `SQLi` level that the `users` table will also have a `password` column too? We can assume the same thing here. Also, note that the source code doesn't show anywhere that the username gets rendered on the screen. It only renders output if the user exists, doesn't exist or there is an error. That means we can't access the password directly and have it render to the site either.

How can we get it? Brute-force!

We can send payloads to uncover information in the database by checking one character at a time. Let's see how the input works by manually testing it a few times before getting into trying to uncover the password.

Just trying the input we'll get what we should expect.

![natas15_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_04.png)

We know we want to discover the password, so let's try to manipulate the query a little.

- `SELECT * from users where username="natas16"`

This is the same as just adding `natas16` to the form username field.

![natas15_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_05.png)

Which responds how we'd expect.

![natas15_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_06.png)

What if we check for a password with some `SQLi`.

- `SELECT * from users where username="natas16" and password like "a"`

![natas15_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_07.png)

Doing this will give us an interesting output.

![natas15_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_08.png)

Let's try to reformat the query then. 

I'm not very good at `SQLi` but after some (quite a bit) research I discovered that the symbol `%` in the SQL syntax we're looking at (`MySQL`) means that it'll match an arbitrary number of characters. To try to avoid more query errors we will also comment out anything after our injected command.

- `SELECT * from users where username="natas16" and password like "%" #`

![natas15_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_09.png)

This will result in an output that will give us hope. Up until now (although I didn't show it) I was doing a lot of research and testing.

![natas15_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_06.png)

The user exists!

The `%` symbol, as noted above, will match an arbitrary number of characters. So, if we're testing a password we should be able to prefix the string with a character and send the payload again. If the string starts with the character(s) we give it, then we should still be told the user exists.

![natas15_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_10.png)

This will result in being told the user doesn't exist. 

But...

![natas15_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_11.png)

This will result in being told that the user does exist! Nice!

That means we can keep checking character after character until we uncover the entire password. If we check a password for a previous level we can see that they are 32 characters long, that means we're going to have to check each of the 32 positions with `a-zA-Z0-9`. That's 62 different characters for 32 positions. That's up to 1984 different attempts until we find the password. I'm not going to do that manually. Let's use `python` to automate the process.

To prevent any case matching issues we'll use the `binary` keyword in our SQL query. This keyword will force the comparison to be case-sensitive.

Our new payload will now be:

- `SELECT * from users where username="natas16" and binary password like "%" #`

OK, let's write the script.

In order to easily grab `a-zA-Z0-9` we can use Python's string module. It has to be imported using the `from-in all` syntax (as seen below).

![natas15_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_12.png)

We'll then setup the script with some variables.

![natas15_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_13.png)

`Characters` is a variable made up of all lowercase (`a-z`) and uppercase (`A-Z`) characters as well as all digits (`0-9`). The variables we're concatenating to make up the `characters` variable all come from the `string` module.

Next, we'll create our session and a list to hold the characters of the password we have found so far.

![natas15_14.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_14.png)

We're going to want to keep looping through each character until we uncover the whole password, so we'll use a `while(true)` loop and handle the exit case at the end of it.

![natas15_15.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_15.png)

Inside the `while` loop we'll step through every character with a `for each` loop. 
When we make the response we'll add the `data` argument in place as we want to change it over and over again. You can see that by using an `f-string` (format string) we can easily form our payload:

- `natas16" and binary password like "<n chars>%" #`

After grabbing the content of the response we'll perform two `if statements`.

![natas15_16.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_16.png)

If the text `user exists` is on the page we know we're on the right track and so we can append the successful character(s) to our `chars_seen` list. We'll use `break` here so we will start from `a` once again. For example, we know the first character is `T` from our manual testing that means the next payload tested will be: `password like "Ta%" #` and not `password like "TU%" #`.

The second conditional is to see if the length of the password we've obtained in `32`. Basically, if we've grabbed the entire password, then print the password to the screen.

The entire script is:

![natas15_17.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_17.png)

Let's run it! It will take a minute so grab a drink.

![natas15_18.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_18.png)

After a few minutes our script will be done and we'll have the next level's password!

![natas15_19.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/natas15_19.png)

It takes ~5 minutes to complete. Nice!

This wasn't that easy and took a lot of research to complete. Writing the script was the easy part. 
