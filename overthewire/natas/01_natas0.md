![natas0_00.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas0_00.png)

For the first level we are given the credentials to access the web page.

When you access the page for the first time you'll be greeted with a basic authentication prompt. Simply add `natas0` as both the username and password.

![natas0_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas0_01.png)

This is the first room so the answer is pretty easy to find. 

On a basic level, what we see when a page loads is made up of `HTML`, `CSS` and `Javascript` (among others) code. `HTML` is like a template for a web page, it contains all the elements that make the layout for all the content that will be presented. `CSS` is for styling (to make things pleasing to the eye), while `Javascript` is used to make websites dynamic and far more interactive. Other languages, such as `PHP` (for one), are server-side languages that can be used to dynamically add content (such as usernames) to the page, to handle web requests and to interact with databases (among others things).

We can find the code for a web page in two main ways. 

First, we can use the `DevTools Inspector` (through `right-click -> inspect`, `F12` or browser options) which will bring up the page's code, sacrificing some of the browser window to allow for the tool panel (which can be moved or popped-out from the window if desired). The `inspector` also allows active manipulation of what the page displays and can also be used to attack poorly configured web pages from the client-side - more on this later. 

Second, we can see a page's source code by right-clicking and selecting `view source` (or using `Ctrl+u`) which will open up in a new browser window. When enumerating web-pages, viewing the source code is one of the first steps taken. Here, we will find the password to the next level.

![natas0_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas0_02.png)

The code in green (between `<!-- -->`) are comments. This is text that won't get displayed on the rendered page to the end user and can useful for developers when trying to make a note of either what their code does (for other developers on a project) or as a reminder to themselves. If you've learned any coding language you'd be familiar with the practice of commenting your code. 

As this level demonstrates, when content is pushed to the public a developer should be cognisant of what comments (if any) get published (even comments about what software version is being used can give a would-be attacker important information). Cases where an oversight may lead to a leak of important/critical/confidential information (through comments in this case) can be damaging to more than just reputation and could be easily avoided by sanitising comments in the source code before the web site is pushed to a live environment. 

