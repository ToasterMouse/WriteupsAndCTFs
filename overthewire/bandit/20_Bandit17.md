![bandit17_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit17_01.png)

Now we need to find the only password that has changed between two files. For this level we can assume that `passwords.new` is simply a copy of `passwords.old` with the one minor change.

We will use the `diff` tool to find out which line is different in `passwords.new` compared to `passwords.old`.

First, let's check out the `diff` tool by reading it's manual.

![bandit17_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit17_02.png)

Finding the password should be a pretty straight-forward thing to do as all we want to know is what line has changed.

We can use the `unified` or `context` switches and set their value to `0` in order to only show the lines that differ (`0` for no surrounding context/lines). It provides an output that is similar to a `git` log which will make it easy to see what has been removed/added.

![bandit17_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit17_03.png)

Here the `-` symbol denotes the `passwords.old` file and `+` denotes the `passwords.new` file. We can see that the lines that differ are supplied after the legend, prefixed with it's relevant notation. Just above this is the line number in the file, again with it's relevant notation (`@@ -42 +42 @@`).

The default readout is like this.

![bandit17_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit17_04.png)

It's a bit less explicit but simply means the file on the left (`<`) has a value at line 42 that differs from the value at line 42 for the file on the right (`>`).

Now we have the password for the next level: `level18 -> level19`.

