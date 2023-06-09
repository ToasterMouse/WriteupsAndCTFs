![[bandit29_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit29_01.png)

Another day, another `git` repository. We'll follow the exact same methodology to clone the repository as we did in the previous levels.

![[bandit29_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit29_02.png)

If we search through the files as we did before we'll see that there is apparently "no password in production".

![[bandit29_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit29_03.png)

The hint here is the word "production".

When it comes to projects that'll be released to the public, typically, developers will work on another branch called a `dev` branch. This is done to compartmentalize the production pipeline so they can make updates and changes in a "safer" environment before pushing the changes to a "live" environment (production). We won't discuss the whole CI/CD pipeline here, but we can assume by the terminology used in this note that there is another branch that is being used for development associated with the repository.

We can check `git` branches with `git branch`.

![[bandit29_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit29_04.png)

There's only one branch here. But that isn't the end of our search because we can also search the remote-tracking branches by using `git branch -r`. These are branches in the local repository that show the remote branch names and maintains a state of what they looked like the last time a user communicated with them.

As per the [git-scm](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches) documentation:

```
If you were working on an issue with a partner and they pushed up an 'iss53' branch, you might 
have your own local 'iss53' branch, but the branch on the server would be represented by the 
remote-tracking branch 'origin/iss53'.
```

After searching with the `-r` switch we find three other branches.

![[bandit29_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit29_05.png)

Here we're going to check out the `origin/dev` branch as this can be a place where some critical information (like passwords) could possibly be stored to make the development process easier. For more context, important information that could be present in a `dev` build are things like a developer OTP (One-Time Password) for quick testing of 2FA/Multi-Factor mechanisms. This is a code that will work no matter what code gets generated for the end-user. They are typically used to make testing simpler and sometimes they unfortunately find their way into production builds. Because of issues like this `dev` builds can be a gold mine for would-be attackers.

We can switch branches by using `git checkout` and then it's name: `git checkout origin/dev`. 

If we check the branch we can see that we're now in `origin/dev`. 

![[bandit29_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit29_06.png)

Now, let's check the logs.

![[bandit29_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit29_07.png)

From the top, we can see in `origin/dev` there is a log with the message "add data needed for development". Data needed? Like a password?

![[bandit29_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit29_08.png)

Yes! Indeed a password!

On to the next one: `level30 -> level31`

