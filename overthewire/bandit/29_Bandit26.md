![bandit26_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit26_01.png)

We have a shell (I hope you haven't closed it yet) as `bandit26` so let's look around for a way to get the password for `bandit27`.

If we list out the current directory we can see another `suid` binary, this time called `bandit27-do`.

![bandit26_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit26_02.png)

It works exactly the same as the previous `do` binary we found. So the method to grab the password for `bandit27` is exactly the same as before.

![bandit26_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit26_03.png)

And we're walkin', again: `level27 -> level28`
