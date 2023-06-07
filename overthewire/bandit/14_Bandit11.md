![bandit11_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit11_01.png)

Now we get to deal with another encoding method called `rot13`. The introduction to the level mentions that the lowercase and uppercase letters have been rotated by 13 positions - a dead giveaway.

`Rot13` basically shifts all characters in the alphabet by 13 places and when it gets to the end of the alphabet the remaining letters wrap around to the start. Like this.

```
abcdefghijklmnopqrstuvwxyz
nopqrstuvwxyzabcdefghijklm
```

This level can be completed easily with the `caesar` tool from `bsdgames`. Which you can get with `sudo apt install bsdgames -y`.

However, it's also achievable with the `tr` tool. This will translate one set of characters to another.

![bandit11_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit11_02.png)

Here we translate the alphabet `a-zA-Z` (uppercase and lowercase) and shift the values by 13 to now follow the convention `n-za-mN-ZA-M` (again, uppercase and lowercase). 

The translation means we shift `a-z` to `n-za-m` which flips the end half (`n-z`) of the alphabet to the front just like the example given above.

Onto the next level: `level12 -> level13`.

