![bandit18_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit18_01.png)

This level is a little more difficult. Now we're dealing with a level that's attempting to keep us out.

![bandit18_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit18_02.png)

As the introduction mentions, this is done by the user having edited the `.bashrc` file.

The `.bashrc` file is a configuration file for a terminal session and can contain things such as aliases, terminal sugar (like in the image below), the path and other configurations that get run when a user logs in.

![bandit18_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit18_03.png)

*terminal sugar made with powerline which is configured through `.bashrc` to run when I log in.*

How are we going to get onto the machine then? 

There is a nifty little trick that we can perform with `cat`. When we run `cat` on it's own, what does it do?

![bandit18_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit18_04.png)

It waits for input. Just like when we run it with `cat -`.

The trick is to simply pipe this to `ssh` for our connection and it'll bypass `.bashrc` and give us command execution.

![bandit18_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit18_05.png)

Pretty cool, right? It simply bypasses `.bashrc` because the nature of `cat` in this state is to wait for input.

If we check `.bashrc` we can see what's going on clearly.

![bandit18_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit18_07.png)

When we login, the `.bashrc` script gets run and it calls `exit 0`, which closes the session. Because `cat` is constantly waiting for input, `.bashrc` never gets to this call.

We can still perform the same thing with our `login.sh` script too.

![bandit18_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit18_08.png)

Even `printf` works for this bypass.

![bandit18_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit18_09.png)

Note that this won't keep the session alive as after `printf` has run it's command, `.bashrc` will run and close the session.

Cool!

Now we can grab the password from the `readme` file that we found!

![bandit18_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit18_06.png)

On to the next level: `level19 -> level20`.
