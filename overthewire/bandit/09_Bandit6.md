![bandit6_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit6_01.png)

This file should be easy for us to find now. We have a basic understanding of the `find` utility and we covered permissions and file ownership in the previous level. 

We are told that this time the file is stored `somewhere on the server` meaning that we will have to search the whole system. This is as simple as changing to the root file directory (not the `root` user's home directory) and using `find`.

Here we will use two new switches `-user` and `-group`. Because we're searching for a file that is owned by `bandit7` and the group `bandit6` we can use these switches to be specific in our search.

![bandit6_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit6_02.png)

The command should be fairly easy to understand now that we did something a little more awkward looking in the previous level. The only new addition is `2>/dev/null`. This is redirecting `stderr` to the `/dev/null` device. Basically, this takes any errors and throws them into the void so they don't clutter our output. We've seen two of the Linux I/O streams so far: `stdin` and `stdout` (input and output). The third of these is `stderr`, for errors. Each of these has an associated number, known as it's file descriptor. These are `0` for `stdin`, `1` for `stdout` and `2` for `stderr`. Therefore, when we say `2>` we are using the redirection operator (`>`) to send the `stderr` stream to `/dev/null`. Which is the Linux device (`/dev`) for nowhere.

Here is what the output looks like without it.

![bandit6_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit6_03.png)

As you can see, it's much cleaner to include it.

To get the password, it's as simple as using `cat` on the file we just found.

![bandit6_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit6_04.png)

Now, on to the next level: `level7 -> level8`.

