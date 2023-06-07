![bandit3_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit3_01.png)

We'll connect to the machine using our script again. The task we need to complete is pretty simple. In fact, we covered finding hidden files in the addendum section of the `Bandit1` write-up so this should be pretty simple.

Once we connect to the machine we will be in the home directory for `bandit3` and if we list it's contents we'll see the `inhere` directory right away.

![bandit3_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit3_02.png)

To change directories we can use the `cd` command and then the name of the directory we wish to move in to, like this: `cd inhere`.

If we only use `ls` to list the directory contents once we've changed to the `inhere` directory, we won't find anything. Just as mentioned in `Bandit1`, we'll use `ls -lah` to list all the files, including the hidden files. To see a list of switches you can use with `ls`, use `ls --help`.

![bandit3_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit03_03.png)

*l = long list format, a = all, including hidden files, h = human-readable format which when used with `-l` will show sizes with suffixes for numbers like 1.0K, 256M, 2G, etc...*

We'll find a file called `.hidden` in this directory that we can read with `cat`.

![bandit3_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit3_04.png)

By now you know the drill. Add the password to a file called `Bandit4` and move to the page for `level4 -> level5`.


