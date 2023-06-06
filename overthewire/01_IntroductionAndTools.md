One of the first things I ever did when it came to anything related to learning about cyber security (aside from playing around with different Linux distributions and learning C++) was to play the `OverTheWire` wargames.

The `bandit` "shell game" was my very first foray into this space and so I thought I should dig out my old notes, update and post them in case they could help anyone else who might be looking from some cool gamified ways to start learning about this field.

First off, here as some tools that I learned about that are useful for the `bandit`.

---

# Initial Tools

#### SSHPass

This tool will allow us to connect to each level without dealing with the password prompt.

**Install:**
- `sudo apt install sshpass -y`

**Usage:**
- `sshpass -p <password> ssh <username>@<domain> -p <port>`
- `sshpass -p password123 ssh ubuntu@10.10.10.10 -p 7777`

**Note:** You must accept the ssh fingerprint which can be done by connecting once to the open port on `bandit.labs.overthewire.org` or by adding: `-o StrictHostKeyChecking=no user@host` as an argument to `ssh`.

---

# Useful command line tools

- `man`: manual pages for linux commands.
- `cat`: concatenate files (open and read contents on stdout).
- `ls`: list files and directories.
- `uniq`: find unique lines from a file.
- `sort`: sort lines in a file.
- `grep`: search for specific characters in an output.
- `strings`: search for human-readable strings in a binary.
- `base64`: base64 encode/decode data.
- `tr`: transform output, can be useful for deleting or shifting characters.
- `tar`: archiving utility similar to 7zip.
- `gzip`: another archiving utility.
- `bzip2`: once again, another archiving utility.
- `xxd`: hexdump a file, or do the reverse.
- `diff`: compare files line by line.
- `tmux`: terminal multiplexer.
- `screen`: terminal multiplexer.
- `ssh`: openSSH remote login client.
- `telnet`: user interface for the TELNET protocol.
- `nc`: connection utility, referred to as the "TCP/IP swiss army knife".
- `openssl`: TLS/SSL command line tool.
- `s_client`: openSSL's connection tool.
- `nmap`: network mapper for scanning open ports on an endpoint.

---

# Useful binary manipulation tools for later stages

- `gef`
- `pwndbg`
- `peda`
- `gdbinit`
- `pwntools`
- `radare2`
- `checksec.sh`
