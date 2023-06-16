![natas18_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas18_01.png)

We have another log in!

![natas18_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas18_02.png)

If we check the source code we'll see a lot but, there are only a few very important lines that will give away the trick to this level.

![natas18_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas18_03.png)
![natas18_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas18_04.png)

I kept all the source code for completeness. However, the most important parts are `$maxid` and the `PHPSESSID` cookie. This cookie simply identifies a user's session ID on a website and can be used to hijack another user's account if there is no encryption (or if an attacker somehow gets hold of it). 

We can see that to beat the level we must login as the `admin` user. That means we will just have to brute-force this cookie (from `0 - 640`). We've done something similar in one of the `bandit` levels.

The `PHPSESSID` cookie will get set when a user logs in. Let's check it out.

![natas18_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas18_05.png)

Once logged in we are told to log in as `admin`.

![natas18_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas18_06.png)

Now, if we check the cookies we can see that we have been given the number, `1`. We're number 1!!!!

![natas18_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas18_07.png)

Note that `PHPSESSID` are rarely ever like this in reality and nor should they be. This form is extremely vulnerable.

We can manipulate this manually but, let's be smart and write a script. It'll be pretty simple.

Here's the script in it's entirety.

![natas18_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas18_08.png)

The first few lines are obvious to us now. The engine of the script should be too but, let's walk through it.

![natas18_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas18_09.png)

We give junk credentials because the site will allow us to login as anything. The reason why we log in is to be able to grab and set the `PHPSESSID` cookie and that's it.

After this we'll loop through all values from `0 - 640` (`$maxid = 640`). We set the cookie to the current iterated value and print to the screen an update of where we're at in the script. Then we make the request with the `auth` and `cookies` arguments and grab the response.

If `You are an admin` is present on the page, which we can see will be our confirmation of logging in (discovered from the source code), we can grab the password.

![natas18_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas18_11.png)

Run the script (it'll take ~40 seconds) and we're onto the next level in no time! This one was much simpler than the last few.

![natas18_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas18_10.png)
