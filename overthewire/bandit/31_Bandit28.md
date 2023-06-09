![bandit28_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit28_01.png)

We have another `git` repository. Let's follow the same methodology we did in the last level.

![bandit28_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit28_02.png)

If we look around we'll find another `README` file but, this time it looks like stuff has been removed.

![bandit28_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit28_03.png)

The `git` tool will allow us to look through commit logs, so we might get lucky and find the password somewhere in an old commit.

We can find what logs exist by using `git log`.

![bandit28_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit28_04.png)

Here the initial commit for the `README` file looks interesting.

We can access each log for more information by using it's commit hash with `git show`.

![bandit28_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit28_05.png)

It's not here either. However, there are more logs for us to check, so let's check them one by one.

![bandit28_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit28_06.png)

Nice! The second log with the message "add missing data" includes the password! We can also find the password in the last log (at the top) with the message "fix info leak".

![bandit28_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit28_07.png)

As you can see, `git` will keep a log of all the commits and any information that was accidentally added will be retrievable through the logs. Firstly, where the data was added and secondly where the data was removed!

Also, note that `git` logs use `diff` as we saw many levels ago!

On to the next level: `bandit29 -> bandit30`

