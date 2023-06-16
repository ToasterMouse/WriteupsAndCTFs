![natas21_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_01.png)

This is an interesting page. The site is `colocated` and we need to login as admin.

![natas21_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_02.png)

Let's check the source code before anything else.

![natas21_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_03.png)

Very bare bones but we can see that like the previous level there needs to be an `admin => 1` key/value pair. 

Let's check the `colocated` domain.

![natas21_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_04.png)

It's a `CSS` style experimenter.

![natas21_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_05.png)

The source code is mostly to do with the style app but, there are some bits that are interesting to us. Specifically, how the code sets `$_SESSION` key/value pairs.

So we are familiar with how cookies are used on these sites, let's check to see what they're like. 

There is a session cookie for this site.

![natas21_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_06.png)

Also for the one we were just on. They're both have a similar format.

![natas21_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_07.png)

Now we have an idea of what's going on, let's start some basic testing.
In the source code for the styler there is a `debug` parameter we can use for discovery. Let's try to use it with an argument to see what happens.

![natas21_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_08.png)

![natas21_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_09.png)

Nothing. Well, not completely. It does print the content of the `$_SESSION` array
Let's check the code again. This section seems particularly interesting to me.

![natas21_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_10.png)

For the form, the `php` checks an array of allowed keys and for each of them (if they exist) the key and value is placed in the `$_SESSION` array. The form is created in HTML and we can see the submit name and value used when it performs a `POST` request. The code to check for "allowed" values sits between the `<form>` tags and is handled inline so, that means we can bypass that code by submitting data to the same endpoint but not through the form.

The idea I had here is to see if we can steal the `PHPSESSID` cookie for `admin` (by injecting `admin => 1` to `$_SESSION`) and then try to use it on the other site. Session cookies maintain a session (who'd have thought that!?), basically, confirmation that you successfully logged in. They are common targets for hackers as they can be used to bypass having to login at all. This behvior is perfect for what we want to do. If we can "log in" here, we can take the cookie and use it for the other site and (hopefully) it'll believe we're now "logged in" there as well.

Before we try the bypass, just so we can be sure how the form works normally, let's submit some changes and then check debug to see what the `$_SESSION` array contains.

![natas21_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_11.png)

We can change the values and they will be reflected in `$_SESSION`. OK.

Let's setup a script.

![natas21_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_12.png)

The usual preamble, this time with the second domain and a version for `debug` along with it.

Let's see what happens when we submit data that's not on the allowed list. We will want to target the `submit` part of the form. The value for that is `Update`.

![natas21_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_13.png)

We will follow a similar methodology as the last level: Send junk data and then check the debug `URL` to see what happened.

![natas21_14.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_14.png)

We can set our own `$_SESSION` variables and don't need the form to do so? Why?

This is why we read the source code very carefully and try our best to follow it's path of execution.

![natas21_15.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_15.png)

If we `submit` something, for each piece of data we send it, it'll create a `$_SESSION` key/value pair. This section of code is above the form in the source and so we can infer that the assumption is that data is only ever sent through the form. Therefore, the code that handles whether a key is allowed will filter out any unwanted data before it can get to this code. Obviously, we can bypass this but, that looks like the intention.

Note that as long as `submit` exists as one of the keys, it'll search through all key/pairs we submit in our request and set the `$_SESSION` variables accordingly.

Once `admin => 1` is set, we want to grab the resulting `PHPSESSID` cookie. Then re-submit the cookie to the first site in order to grab the password for the next level.

![natas21_16.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_16.png)

The `requests` module makes grabbing session cookies easy with: `session.cookies["<COOKIE_NAME>"]`.

Once we have everything in place we'll make a `GET` request to the first site and carve out the password.

![natas21_17.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_17.png)

We can also grab the `PHPSESSID` cookie and perform the last stage of the attack in the browser.

![natas21_18.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas21_18.png)
