![bandit0_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit0_07.png)

We can find the password for `level1` in the `readme` file. This file exists in the `bandit0` user's home directory.

When logging in over SSH we will typically (unless otherwise configured) be placed in the home directory of the user we logged in as. To confirm that we're in the right place we can find our location by using the present working directory utility: `pwd`.

So, to complete this level we will:
1. find where we are: `pwd`.
2. list the directory we are in: `ls`
3. open the file found we found: `cat`

![bandit0_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit0_04.png)

Now we have the password we'll add it to a file called `bandit1` and head over to `level1 -> level2`.

---

# Addendum

We don't need to use all of the other tools supplied above but knowing them can be very useful. 

For example, `du` is used to estimate the size of a file or directory. To see this in practice, let's check the size of `readme` and the size of the `/home/bandit0` directory. 

**Note:** We can use the `man` command, followed by a utility name to give us the user manual for the tool we want to use. We can also list the `help` for a tool either by using `-h` or `--help`, depending on how the author configured the tool.

![bandit0_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit0_06.png)

*Using the switch/flag/tack `-h` will give an output that is "human-readable"*

If there is only one file in the directory then why is the directory size `20K` and not `4.0K`?

We can discover why by using the `ls` tool with the switches `-lah` which will list all the files, including hidden files, in a human-readable long list format.

![bandit0_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit0_05.png)

Files that are prefixed with a `.` are hidden files. All of these files are estimated by `du` to be `4.0K` in size. So, these, including the directory itself adds up to `20K`. This is just one example of the importance of knowing your tools. It may not be completely obvious why this particular tool could be useful but when you need it, you'll be glad you know it.




