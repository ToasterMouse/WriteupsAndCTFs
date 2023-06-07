![[bandit4_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit4_01.png)

For this level we are tasked with finding the only human-readable file in a directory.

Once on the machine as `Bandit4` we will find a directoy called `inhere`. Inside this directory we'll find a bunch of files. 

![[bandit4_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit4_02.png)

We can search for files using the `find` utility. If we check the `help` for `find` we can see that it has a flag to list `readable` files.

![[bandit4_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit4_03.png)

However, this means files that can be read by the current user, which is all of them in this directory. Therefore, it's not helpful for us in this case, however, it's a good switch to know. 

So, how are we going to find the right file? Most of the files here contain junk data and so we can assume that "human-readable" means a file with content that is legible.

When we think through the problem, we may ask ourselves: What type of file is used pretty much every day for notes or reminders? A text file, right? Text files typically contain plaintext data, that means we should look for a file that contains plaintext ASCII.

We can find the data type of a file by using the `file` utility. Because we have multiple files we can either manually check them one by one, with a loop, a script or with the `find` utility's `-exec` switch. The `-exec` switch will perform an action on each file.

![[bandit4_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit4_04.png)

The above command uses `find` on the current directory and executes `file` on each file in the directory. The `{}` is a placeholder for the name of the current found file and `\;` is an escape for the `;` bash operator and is necessary for the command to run properly.

Now we can see all the data types of the files in the current directory and `./-file07` looks promising. 

![[bandit4_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit4_05.png)

Nice! Now we have the password and can move onto the next level: `level5 → level6`.





