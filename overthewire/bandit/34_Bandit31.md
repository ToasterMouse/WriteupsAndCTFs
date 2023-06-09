![bandit31_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit31_01.png)

More `git`!

Here we are told to push a file called `key.txt` with the content `May I come in?` to the repository. This shouldn't be too hard.

![bandit31_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit31_02.png)

If we check the repository we'll see that it contains a `.gitignore` file that includes `*.txt`. That means it'll not track text files when we try to add files to the repo. However, we can override this when adding the file by using the `-f` switch.

So, first, we'll create our `key.txt` file and add it to the repository.

![bandit31_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit31_03.png)

Second, we'll make a commit. We can add a message for this commit with the `-m` switch.

Before we commit the file we can check the status of our repository by using `git status`.

![bandit31_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit31_04.png)

We can see that we have a new file that needs to be committed.

![bandit31_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit31_05.png)

At this point we should have checked the branches already if we have followed the methodology we've been building up until this point.

![bandit31_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit31_06.png)

In doing so we would have found a `master` branch. We want to push our commit to this branch which we can do with `git push`.

If we check the branches again (this time using the `-a` switch for listing all remote-tracking and local repositories) we'll find the full name for `master`.

![bandit31_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit31_07.png)

Why did we do this? Well, we wanted to know the full name for the `master` branch as `push` needs us to specify where to send our commits. 

`master` is in `origin` (the name for the remote repository that we cloned from). Therefore, when we `push` to `master` we want to do it like this: `git push origin master`.

We'll get prompted for the password again (which is the same as the password for `bandit31`).

![bandit31_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit31_08.png)

There's the password!

On to the next level: `level32 -> level33`.
