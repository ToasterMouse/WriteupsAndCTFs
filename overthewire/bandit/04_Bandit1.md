![bandit1_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit1_01.png)

For this level we will use some basic Linux commands and their switches to find a file called `-`. 

We can use our login script (after we've added the password we found to a file called `bandit1`) like this: `./login.sh 1`

![bandit1_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit1_02.png)

In the write-up for the previous level I mentioned the use of `ls` to list the files in a directory and how we can discover hidden files using the `-lah` switches. The file `-` won't be hidden, but using the switches when we want to list directories will always give us a fuller picture of the directory we're in.

![bandit1_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit1_03.png)

We can see the file called `-` at the top. However, if we use `cat -` the utility will do something odd. 

![bandit1_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit1_04.png)

This is because `-` has a specific use case for `cat`, which is to read data from the `stdin` (input) stream. That means when we use `cat -`, the `cat` utility will wait for input and then echo the same data back to us.

To get out of this loop we'll use `ctrl+z`.

We can confirm that this is the default behavior for `cat` by checking it's help page.

![bandit1_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit1_05.png)

How can we read the file?

First, it's important to know that there are two common ways to access files. These are either by their `absolute` or `relative` paths. 

The `absolute` path is the path from the root system (sometimes called the "top") directory, to the file (here that is `/home/user/bandit1/-`). A relative path is a path to a file from the current location on the file system. When using a file's `relative` path the current directory is specified by a `.` and the previous directory is specified by a `..`. 

If we specify the current directory with `./` we can add the filename like this: `./-`. Now we will be able to read the file as `cat` will expect a file called `-` rather than waiting to read from `stdin`.

![bandit1_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit1_06.png)

Add this password to a file called `bandit2` and let's move on to `level2 -> bandit3`.



