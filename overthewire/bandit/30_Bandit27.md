![bandit27_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit27_01.png)

There's a `git` repository on the `bandit` machine that we can access using the address provided in the instructions. We can clone `git` repo's using the `git` tool's `clone` module.

First, we'll need to create a new folder in `/tmp` to clone the repository.

![bandit27_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit27_02.png)

Now, we'll `cd` to the directory and use `git clone` with the address from the introduction, making sure to add `:2220` for the port after `localhost`.

![bandit27_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit27_03.png)

The password is the same as the password for `bandit27`.

Now the repository will be cloned to our new directory.

Inside the `repo` directory we'll have a `README` file that includes the password.

![bandit27_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit27_04.png)

On to the next level: `level28 -> level27`.
