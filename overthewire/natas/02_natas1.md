![natas1_00.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas1_00.png)

We don't get an introduction to the level as we do with `bandit` so we'll have to discover things as we go using some basic web enumeration techniques.

First, we'll head over to the new page and enter the username and password for the level in the basic authentication prompt.

![natas1_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas1_01.png)

Now, we'll be told a similar thing to the last level but, right clicking has been disabled. 

![natas1_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas1_02.png)

Remember, that there is more than one way to get to the source code. We can use `inspect element` through the brower's `DevTools`. Or, we can use the `view source` option.

Interestingly, when using `Firefox` on `Kali Linux`, right-clicking was only disabled when trying to do so on the `<div>` containing the text. Just below it, we can still view the source by right-clicking.

![natas1_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas1_04.png)

We will find the password in the exact same way we did in the first level.

![natas1_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas1_03.png)
