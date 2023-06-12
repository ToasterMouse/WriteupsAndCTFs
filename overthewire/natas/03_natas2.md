![natas2_00.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas2_00.png)

Apparently, there is nothing on this page.

![natas2_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas2_01.png)

If we check the source code we'll find an image.

![natas2_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas2_02.png)

A simple pixel but, what else is noticeable is that we can see a directory called `files` where this image is stored. Let's see what's there.

![natas2_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas2_04.png)

We can access the directory listing by traversing to `/files` and find another file called `users.txt`. 

What does that contain?

![natas2_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas2_05.png)

Passwords!
