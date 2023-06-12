![natas5_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas5_01.png)


Although we'll start to us `Python` more and more from here on out, it's a good idea to not just stay in sublime-text but go out to the page to do a little recon first off.

![natas5_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas5_02.png)

Apparently, we need to log in?

We could look for a `login` portal of some kind, however, let's try something a little simpler. 

What is a way for websites to track a visitor/user? `Cookies`, right?

Let's have a look to see if there are any `cookies` that have been set. We can do that through `DevTools` in the `Storage` tab and then open the `Cookies` section on the left side panel.

![natas5_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas5_03.png)

We can see that there is a `cookie` tracking whether someone is logged in. However, it's set to `0`. 

In programming, `true` and `false` have historically been defined by `0` and `1` before the introduction of `boolean` variables. In many languages the convention of using `0` for `false` and `1` for `true` is still followed.

So, what if we change the cookie to `1` and refresh the page?

![natas5_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas5_04.png)

Now, we're considered logged in and we are given the password.

---

# The Python Way

Here's a script I used to perform the same action on the page. I commented what is occurring at each step. Note the use of `session.get()` rather than `requests.get()`.

![natas5_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas5_05.png)

Technically, for this level `requests.get()` will work. However, it is good to get used to working with `sessions` as well as working with `requests`.
