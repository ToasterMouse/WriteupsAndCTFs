![bandit9_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit9_01.png)

Another file called `data.txt` and this time we need to find the only human-readable string that has a few `=` characters at the start.

We can do this pretty easily with `grep`. We touched on `regex` pattern matching in `bandit7` and we can use this to our advantage here.

![bandit9_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit9_02.png)

Here we use `-o` for showing only non-empty lines that match the pattern we're searching for and `-E` for extended `regex` matching. However, as you can see the file isn't an ASCII text file.

This is where the `strings` utility comes in handy. This is a tool that can print human-readable strings inside a binary file to `stdout`.

We can then pipe that output to `grep` to match the pattern we supply. The above pattern means to return lines that include any number of `=` characters. In `regex`, `.` is to match a character; `.*` is to match any number of a character.

![bandit9_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit9_03.png)

Now we can move on to the next level: `level10 -> level11`.
