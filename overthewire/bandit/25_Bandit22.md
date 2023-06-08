![bandit22_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit22_01.png)

On this level we're looking at another `cronjob`.

![bandit22_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit22_02.png)

This job is similar to the previous level. It runs every minute as the next level's user.

![bandit22_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit22_03.png)

This is a pretty simple script to understand but let's break it down.

```bash
# grab the current user's name, this will be bandit23
myname=$(whoami)

# 1. echo the string "I am user bandit23"
# 2. create a md5 hash from this string and remove extraneous output with cut
# 3. $mytarget is set to: 8ca319486bfbbc3663ea0fbe81326349
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

# copy the password through redirection to the file /tmp/8ca319486bfbbc3663ea0fbe81326349
cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

That means to find the file we want to read we simply need to convert "I am user bandit23" to a MD5 hash. We can do this with the `md5sum` tool.

![bandit22_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit22_04.png)

We can confirm it exists and that we can read it with `ls -lah`.

![bandit22_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit22_05.png)

Of course, we want to read it too.

![bandit22_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit22_06.png)

And we're walking: `level23 -> level24`
