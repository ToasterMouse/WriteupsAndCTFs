![bandit14_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit14_01.png)

We should still be on the machine as `bandit14` from the previous level. 

The introduction says that we have to submit the password to port `30000` on `localhost`.

This shouldn't be too difficult. We can connect to local ports by using the `nc` (netcat) utility. To achieve this we can either connect and then copy and paste the password. Or, we could use `echo` and pipe the password to `nc`.

![bandit14_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit14_02.png)

Now we can head over to the next level: `level15 -> level16`.


