![bandit8_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit8_01.png)

Now we need to search a file for a line of text that occurs only once. This isn't too hard and to do it we'll use some tools we've used before and two new ones: `sort` and `uniq`.

If we want to find unique lines we will use the `uniq` tool. However, if we check the `man` page we can see something interesting.

![bandit8_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit8_02.png)

It filters adjacent matching lines. That means if a line appears more than once but is separated by non-matching lines `uniq` will not provide us an accurate output.

![bandit8_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit8_04.png)

We know that the file contains multiple lines that are the same (we can infer that from the instructions) but the output above is showing us that it's finding many lines that only appear once. 

This is where the `sort` utility comes in handy. It'll sort the file and place all lines in order. Meaning matching lines will be bunched together.

![bandit8_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit8_03.png)

Here we open the file and pipe the result to `sort`. Then we call `uniq` to show us the number of times the line was found (`-c` is the switch for count) and finally we pipe the results to the `head` utility which will only print a select number of lines from the top of the output/file that we specify by using `-n`.

In order to get truly unique lines from `uniq` we use the `-u` switch.

![bandit8_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit8_05.png)

Now we have the next password and can move to the next level: `level9 -> level10`.

