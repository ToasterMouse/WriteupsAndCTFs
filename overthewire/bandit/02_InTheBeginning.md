To begin we'll head to [OverTheWire](https://overthewire.org/wargames/).

![01intro](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/01intro.png)

Then we'll select the first link on the menu to the left: `bandit`.

![02levels](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/02levels.png)

Make sure to read the `Note for beginners` section as it will help you get started. The idea here is that we need to complete a level to get the password to the next level.

We can start the first level by selecting it from the menu on the left.

Each level will give you an introduction and a hint for the commands you should use as well as some useful references.

Let's begin with the initial setup and connection.

---

![bandit0_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit0_01.png)

The first level simply wants us to learn how to connect to `bandit.labs.overthewire.org` on port `2220` and it gives us the username and password `bandit0:bandit0`.

---

### Initial setup

To make it easier to connect to other levels in the future we'll first create a simple script. You can achieve the same thing on the command line, however, this will just save us some typing.

```bash
#!/bin/bash
sshpass -p $(cat bandit$1) ssh bandit$1@bandit.labs.overthewire.org -p 2220
```

We'll save it as `login.sh` and make it executable with `chmod +x login.sh`. 

We can use it like this:

```bash
./login.sh <room_number>
```

All we are doing is using `sshpass` to provide the level password from the command line and then connecting to the open port on `bandit.labs.overthewire.org`.

The only requirement for the script is to set the password to a file that we will name `bandit<n>`. For example, we'll set the password `bandit0` to a file called `bandit0` for the `bandit0` level.

---

### Level: `Bandit0`

We will have an issue with the SSH `fingerprint` when using the script for the first time, so we will accept it by either connecting and accepting the fingerprint...

![bandit0_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit0_02.png)

...or we can use the option switch: `-o StrictHostKeyChecking=no` for the SSH utility. After this initial connection it'll be possible to use the script without issue. If you come back to these levels after some time away you may need to repeat this step.

![bandit0_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit0_03.png)

That's all we need to do here. Now we'll move on to `level0 -> level1`.
