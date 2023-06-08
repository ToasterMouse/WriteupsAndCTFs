![bandit19_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit19_01.png)

This level wants us to use a provided `setuid` binary to access the next level's password file.

The `setuid` bit, denoted as an `s` on file permissions, is a bit that will allow a file to be run as the owner of said file.

A common Linux binary that has this permission set (and has previously been abused as a vector for privilege escalation via a vulnerability named `pwnkit`) is `pkexec`. It's a binary that's like `sudo` as it lets users run programs as another user through `polkit`, which controls access of unprivileged processes to privileged processes (system-wide).

![bandit19_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit19_02.png)

Note the permissions for the `owner` of the file (`rws`). The `s` signifies that the `SETUID` bit has been set and so it will run as `root`.

On the `bandit` machine we can only read the password of our current user, however, with a `setuid` binary it may be possible to read the password file of another user if that binary runs with permissions that give us elevated access (either as `root` or the relevant user).

![bandit19_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit19_03.png)

We can see that the binary is nicely highlighted for us. It's owner is `bandit20` and we can use it as the `bandit19` user. Also, it's name `bandit20-d0` makes it pretty obvious that it allows us to perform actions as `bandit20`.

First, we'll run it to see what happens.

![bandit19_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit19_04.png)

It allows us to run commands as `bandit20` as we expected.

![bandit19_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit19_05.png)

If we run the example command we can see that the effective UID (`euid`) when we run the binary is the user id for `bandit20`. That means any actions performed through this binary will be performed with the privileges of the `bandit20` user. So, let's try to read the `bandit20` password file.

![bandit19_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit19_06.png)

It's as simple as that. This is actually an example of an attack commonly referred to as lateral privilege escalation where we move or perform actions as another user on a system.

Onto the next level: `level20 -> level21`.

