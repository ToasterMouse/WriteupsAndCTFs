![bandit2_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit2_01.png)

As with previous levels we will use our script to connect to the new level. Here the user is `bandit2` so we use the script like this: `./login.sh 2`

Once on the machine we'll find a file called `spaces in this filename`.

![bandit2_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit2_02.png)

Because `cat` will separate files by spaces, it will not understand how to read files with spaces in their name when we try to read it like this: `cat spaces in this filename`.

![bandit2_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit2_03.png)

As you can see it expects four files. Each called `spaces`, `in`, `this`, `filename` respectively.

We can read filenames with spaces in two ways. First, by using the escape character `\` to escape spaces, like this: `cat spaces\ in\ this\ filename`. However, this can be tedious with longer file names. We can also read files by surrounding the filename with quotes.

![bandit2_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit2_04.png)

Once again, add the password to a new file (this time called `bandit3`) and we'll move on to `level3 -> level4`.

