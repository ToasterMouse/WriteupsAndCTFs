![[bandit30_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit30_01.png)

More `git`. You know the drill!

We'll not find much other than getting taunted by the repository! How dare it!

Looking through the `git` manual we'll find that we can check to see if there are references to repositories by using the `git-ls-remote` command.

![[bandit30_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit30_02.png)

It also has it's own manual that we can look through.

![[bandit30_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit30_03.png)

It can be used like this: `git ls-remote`. It will require us to enter the password again.

![[bandit30_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit30_04.png)

Once it's done it'll return two references to us. The one called `refs/tags/secret` piques my interest right away!

We can access them by using `git show`.

![[bandit30_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit30_05.png)

There's the password!

On to the next level: `level31 -> level32`.
