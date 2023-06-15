![natas17_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas17_01.png)

We have another username input to check.

![natas17_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas17_02.png)

This time when we check the source code we can see that it's not going to output anything to the screen. This may seem impossible but, we have many tricks up our sleeves.

![natas17_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas17_03.png)

This is a truly blind SQLi attack because, we don't have anything relayed to us to tell us if we're successful or not. However, even though this is the case we can still discover if we're successful in finding characters in a password.

We can do that by using a time-based attack.

In this attack, we can force the server to run the `sleep` function to let us know if we are successful when brute-forcing the password. If we are successful with our command (it returns `true`) then we can expect the response to take some time to complete. If it's `false` then we'll get a response almost right away.

We can use the `sleep` function by passing it the number of seconds we wish to wait. `5` seems good enough, so we'll use `sleep(5)`.

The payload we'll form is very similar to our previous `SQLi` attack:

- `natas18" and (select sleep(5) from users where binary password like "%") #`

All we've changed is we've use parentheses to group the second `select` command with a call to check the password and added `sleep(5)`. On success the server will wait 5 seconds before sending the response.

If we test the payload on the website, we'll find that the time-based attack works.

![natas17_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas17_04.png)

We also get nothing returned to us.

![natas17_05png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas17_05.png)

In reality the response will take a little longer than 5 seconds so, we can test how long the response took by using some python `requests` module response object methods.

Our script:

![natas17_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas17_06.png)

As you can see it's very similar, we just update our payload to use `sleep(5)` in a separate `select` statement. We'll also have to make sure that our quotes are in order so the command gets executed properly (this will require a little character escaping). 

We test if we were successful with a conditional that will only be `true` if the response took longer than 5 seconds, by using `response.elapsed.total_seconds() > 5.0`.

This will take ~8 minutes to finish.

![natas17_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas17_07.png)

After some time we'll get the password and can move on to the next level. Phew! They've been getting harder, no?
