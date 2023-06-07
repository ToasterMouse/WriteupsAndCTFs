![bandit13_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit13_01.png)

Here we need to find an SSH private key. 

SSH keys come in pairs with a private and a public key. They are used as a password-less (although keys can be password protected) method of authentication for establishing encrypted communication between a client and server. With a private key, we can access a SSH session as long as it contains it's public key counterpart as an `authorized key` (PubkeyAuthentication must also be set in the `sshd` config file), which is held in the `.ssh/authorized_keys` file in the relevant user's home directory. As per it's name, if you are using key-pair authentication, you don't want to give out your private key to anyone as it should remain private.

For this level, as soon as we log in we will find it.

![bandit13_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit13_02.png)

Cool! To get the password that is in `/etc/bandit_pass/bandit14` we need to use this key to log in as the `bandit14` user. This time we won't use the `login.sh` script.

First, we'll need to copy the content of the key to a new file we'll call `id_rsa13` (`id_rsa` is a typical name for a private key. `id_rsa.pub`, for a public key). Then we'll use the `chmod` utility to set it's permissions correctly. For private keys, these permissions are read and write only for the owner of the file. We can do this with `chmod 600 id_rsa13`.

If we check the help for `ssh` we can see how to use the private key file.

![bandit13_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit13_03.png)

To use the file with `ssh` we need to use the `-i` switch.

![bandit13_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit13_04.png)

Now we can find the password in `/etc/bandit_pass/bandit14`.

![bandit13_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit13_05.png)

We can stay on the current machine as `bandit14` for the next level: `level14 -> level15`.

