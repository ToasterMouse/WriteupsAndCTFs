![bandit23_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit23_01.png)

Once again we're looking at a `cronjob` and this time we're told that we have to write our own shell script. Nice!

![bandit23_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit23_02.png)

Let's check out the script running in the `cronjob` first.

![bandit23_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit23_03.png)

It is a little more complicated than the last script but if we go through line by line it will start to make sense. Remember, the script is run as `bandit24`.

```bash
# $myname=bandit24
myname=$(whoami)

# go to /var/spool/bandit24/foo or exit if it doesn't exist
cd /var/spool/$myname/foo || exit 1

# for any file with any extension in the folder
for i in * .*;
do
	  # if the file isn't called '.' and it isn't called '..'
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        # find the owner of the file
        owner="$(stat --format "%U" ./$i)"
        # if the file is owned by bandit23
        if [ "${owner}" = "bandit23" ]; then
		    # run the file, wait for 60 seconds and then kill the process 
            timeout -s 9 60 ./$i
        fi
        # delete the file
        rm -rf ./$i
    fi
done
```

The call to `timeout` makes this script vulnerable because it will run any file that exists in the `bandit24` spool folder.

If we check the permissions we can see that `bandit23` can `read` and `execute` on the `bandit24` directory and `write` and `execute` on the `foo` directory.

![bandit23_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit23_04.png)

That means we should be able to place a script inside the `foo` directory and have it read the `/etc/bandit_pass/bandit24` file and redirect the password to another file that we have permission to read.

To do this, we'll use the `/tmp` directory.

First, we'll create a new folder in the `/tmp` directory called `henlo` and make it `rwx` for everyone by using `chmod`.

![bandit23_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit23_05.png)

Now everyone can read and write to this directory. 

Let's create our shell script in our new directory and then move it to `/var/spool/bandit24/foo`. 

We can make a script by using this quick one-liner:

```bash
echo -e '#!/bin/bash\ncat /etc/bandit_pass/bandit24 > /tmp/henlo/passwd' > /tmp/henlo/script.sh
```

The `-e` switch for `echo` will allow for parsing of things like newline characters (`\n`). This formats our script so that the hashbang/shebang (`#!`) is on it's own line. After the newline character all we're doing is opening the `bandit24` password file and redirecting the output to a file in the directory we just made. Finally, we redirect the code to a file we'll call `script.sh`.

![bandit23_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit23_06.png)

If we `cat` the script, it'll look like this.
```bash
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/henlo/passwd
```

Next, we'll make the script executable with: `chmod +x script.sh` and then copy the file using the `cp` utility.

![bandit23_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit23_07.png)

Because the original script that runs from the `cronjob` runs every minute, we'll have to wait a bit. We can check the time with the `date` tool and after a minute turns over a new file will be created in `/tmp/henlo`.

![bandit23_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit23_08.png)

Read it to get the password!

![bandit23_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit23_09.png)

On to the next level: `level24 -> level25`
