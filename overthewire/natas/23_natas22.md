![natas22_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas22_01.png)

Another page but, nothing here?

![natas22_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas22_02.png)

Source code check, awaaaaay!

![natas22_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas22_03.png)

This is a very simple bit of code. 

```php
if(array_key_exists("revelio", $_GET))
```

So, if there is a parameter to the page called `revelio` we will be given the password.

But it's not `revelio'ing` the password when we try to access the page with it... why's that?

Let's check the `network` traffic.

![natas22_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas22_04.png)

It responds with a `302` status response. What is that? You may know. It's a redirect.

![natas22_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas22_05.png)

That means when we go to the page we can see that it redirects us back to `/`. How can we prevent redirects?

We can use the python argument to the requests module: `allow_redirects=False`.

![natas22_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas22_06.png)

We can do this with a simple request and disallow any redirects. Then the page will have the password on it that we can use `regex` to carve out! 

A nice and simple level!
