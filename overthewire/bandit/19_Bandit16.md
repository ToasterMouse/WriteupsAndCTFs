![bandit16_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit16_01.png)

This level is a little more in-depth than the last one. It requires us to find a port that "speaks" SSL in the range of `31000 - 32000`. Then, we need to send the current user's password to that port.

That means we need to perform port scanning to discover what, if any, services are running on any ports in the given range. We can do this with the network mapper utility: `nmap`.

If we check the help and/or manual for the `nmap` tool we can find some useful options.

![bandit16_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit16_02.png)

It's a pretty large readout but if we scan through it carefully we will find that it's possible for us to scan a range of ports on an address by using the `-p` flag. We can run a default script scan with `-sC` and do service/version discovery with `-sV`. Furthermore, we can specify how fast we wish the scan to go by using the `-T` (timing template) switch and supplying an argument between `0-5` (`0` being the slowest and `5` the fastest).

Now, we will perform a scan using `nmap` with the switches I mentioned above.

![bandit16_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit16_03.png)

As you can see there are a few ports that are open in the range we specified: `31046`, `31518`, `31691`, `31790` and `31960`. The most interesting one for us is `31790`.

![bandit16_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit16_04.png)

Not only is it using `SSL` it is also leaking to us that it expects to receive the current user's password. Therefore, we can do the exact same thing we did in the previous level. This time with the port we just discovered.

![bandit16_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit16_05.png)

Success! This time we are given a private key!

![bandit16_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit16_06.png)

Copy this key to a new file (call it `id_rsa17`) and use `chmod 600 id_rsa17` to set the correct permissions on the file.

Now we can move on to the next level: `level17 -> level18`.
