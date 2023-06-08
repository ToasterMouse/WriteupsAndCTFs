![bandit15_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit15_01.png)

Similar to the previous level we have to make a connection to a listening port on the `bandit` machine. This time it expects an encrypted connection.

We can achieve this in a similar manner to how we made a connection with `netcat`. This time we will use the `openssl` tool.

![bandit15_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit15_02.png)

The `s_client` module for `openssl` will allow us to create a secure connection using the `-connect` switch.

![bandit15_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit15_03.png)

The level wants us to send the `bandit15` user's password via this connection. We can either copy it from our `bandit15` file or we can find it on the `bandit` system in `/etc/bandit_pass/bandit15`. As we are currently the `bandit15` user, we will have access to this file.

![bandit15_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit15_04.png)

Now, all we have to do is pipe the password to the open port (30001).

![bandit15_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit15_05.png)

This will connect to the port however we don't find a password!? When we read the instructions we can see that OTW suggests we use `-ign_eof`.

![bandit15_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit15_06.png)

This switch will ignore the input eof (End of File). Meaning, the returned output should contain a bit more stuff.

Run the same command again but this time add `-ign_eof` and we should see some more data returned to us before the connection is closed.

![bandit15_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit15_07.png)

Nice! The password was cut off in the original output but if we ignore the input end of file the connection isn't closed before it returns the password to us.

On to the next level: `level16 -> level17`.
