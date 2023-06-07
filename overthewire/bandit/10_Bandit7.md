![bandit7_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit7_01.png)

The explanation for this one seems a little vague at first but if we check the commands that can be useful we can see `grep`. This is a pattern matching tool which means we can search for data in files that match a pattern we supply. 

On the machine we can list the directory and find `data.txt`.

![bandit7_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit7_02.png)

If we `cat` it we will find that the file is HUGE.

![bandit7_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit7_03.png)

We can see that a keyword is followed by a string of randomized text. That means we need to find the word `millionth` and the string of text next to that will be the password!

Searching files with `grep` is very easy and it's a tool that you will use over and again. There are two common ways to `grep` a file. We can either pipe the output of a file (from `cat`) to `grep` or we can add the file as an argument to `grep`.

Piping, basically, is a way to send the output of an operation to the input of another. It is done in Linux `bash` by using the `|` operator.

![bandit7_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit7_04.png)

Of course, there are many switches that we could use to make the `grep` action more specific. A common switch is `-i` which means we're searching in a case-insensitive manner and either `-e` or `-E` for `regex` or `extended regex` pattern matching, respectively.

You can check the `grep` utility's `help` and `man` page for more.

Now onto `level8 -> level9`.

