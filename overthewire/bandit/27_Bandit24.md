![bandit24_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit24_01.png)

This level sounds really interesting and introduces a new concept called `brute forcing`.

If you are unfamiliar, `brute forcing` (in a general sense) is the act of guessing a combination, in a methodical manner, until one of the guesses works to open the mechanism being "bruted".

For example, a four-digit combination lock has a total number of 10,000 possible combinations but can easily be brute-forced by testing one combination after another (although this can take upwards of 30 minutes or more to be successful). As an aside, to find the total number of possible combinations we take the number of values (0-9 = 10, per number wheel/disc on a physical lock) to the power of the number of wheels/discs (4).

On this level we will need to perform this exact type of brute-force attack but thankfully with the power of a computer it will take a fraction of the time compared to manually testing a physical lock.

Before we get too ahead of ourselves, let's do a little recon to find out how to interact with port `30002` on the `bandit` machine. We will use `nc` to communicate with the daemon.

![bandit24_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit24_02.png)

We can see that it expects the password and then the pin code separated by a space. Cool, now we know our goal and how to interact with the daemon, how can we do that over and over again without typing out the password and pin every time? Automation! Let's write a short bash script to do this for us.

By using a for loop we can iterate from `0000` to `9999` and on each iteration send the listener the password plus the pin using `echo`. Like this:

```bash
for i in {0000..9999}; do
	echo "VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar" $i;
done
```

First, we'll have to make a new folder in `/tmp`, it can be named anything. I just went with `lol`. Then we'll open and create a new file with `vim connect.sh`. From here, we'll press `Insert` to enter `insert` mode and then use `Ctrl+Shift+V` to paste the script. Now, we'll press `Esc` to exit `insert` mode and then use `:wq` to save and exit from `vim`. Lastly, we'll make the file executable with `chmod +x connect.sh`. 

![bandit24_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit24_03.png)

We'll quickly check our script to make sure it looks right.

![bandit24_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit24_04.png)

Now we will pipe the output of our script to the listener!

```
./connect.sh | nc localhost 30002
```

Keep calm and let it do it's thing!

![bandit24_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit24_05.png)

If everything is correct we'll eventually be given the password for the next level!

And we're walkin': `level25 -> level26`.

