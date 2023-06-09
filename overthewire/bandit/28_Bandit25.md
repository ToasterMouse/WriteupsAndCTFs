![bandit25_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_01.png)

This one is super simple... well the `bandit25` part is. We'll login to `bandit25` and find a private ssh key for `bandit26` straight away!

![bandit25_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_02.png)

By now you know how to use these. 

But, hold on!

We're told that the `shell` for `bandit26` isn't `bash`, let's find out what it is. We can find out what shell a user has by reading the `/etc/passwd` file.

![bandit25_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_03.png)

It's a shell called `showtext`. What is that? Let's do some investigation.

![bandit25_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_04.png)

It's a shell script that sets the terminal to `linux` and runs `more` which will open the file `text.txt` in the home directory for `bandit26`, then it exits.

The `more` tool is a file viewer that will buffer the output when it's too large to fit on the screen. It pages the output so we can easily scroll through it with the arrow keys. There is also another Linux tool, `less` that does a similar thing. Interestingly, the `more` utility isn't as complete when compared to `less`. Even the `man` page for `more` notes that less is more.

![bandit25_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_05.png)

Cool! So what can we do here? I'm going to introduce you to one of the most helpful tools/sites for binary bypasses in Linux: [GTFObins](https://gtfobins.github.io/).

If we use the search feature to look for the `more` binary we'll see that it has an entry.

![bandit25_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_06.png)

When reading the entry we can see it's possible to use commands in `more` to spawn a new shell. It can even be used to perform privileged reads.

![bandit25_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_07.png)

When we login to the machine as `bandit26` the `text.txt` file is output with the typical `ssh` connection preamble and the connection is closed. `more` isn't getting used as the output for `text.txt` isn't big enough to buffer.
We need to force `more` to buffer the output of `text.txt` so we can use it's ability to issue commands. The easiest way to do this is to make the output in your terminal really large. We can do this with `Ctrl Shift +`.

![bandit25_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_08.png)

Now try to access the machine.

![bandit25_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_09.png)

If it's large enough we'll see `more` start to buffer the output (as indicated by the percentage at the bottom). Now let's try to spawn a new shell process with the command we found on `GTFObins`.

![bandit25_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_10.png)

It doesn't work!? What do!?

Let's read the `more` documentation to see how it works when it pages data. After a bit we'll see something really interesting.

![bandit25_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_11.png)

We can start an editor with `v` which defaults to the `vi` editor. Checking for `vi` on `GTFObins` we can see that it too has an entry and a method to spawn a new shell process.

![bandit25_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_12.png)

Because we'll be inside the `vi` editor when using the `v` command from `more`, we'll use the second method.

So, when we're at the point where `more` is paging the output...

![bandit25_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_13.png)

... press `v`.

![bandit25_14.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_14.png)

At this point we can zoom out to make things easier to see and follow the method on `GTFObins`. First, we set the shell with `:set shell=/bin/bash` and then we run the shell with  `:shell`.

![bandit25_15.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit25_15.png)

We have the password but we know that trying to login as `bandit26` results in us being kicked out so we're going to have to try something else. 

Stay in this shell for the next level: `level26 -> level27`
