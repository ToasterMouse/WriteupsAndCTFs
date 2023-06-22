![[crackthehash_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_00.png)

These become easier to recognize the more you deal with them so don't worry about being able to know what hash you're looking at straight away. 

There are tools that can help you identify hashes like `hashid` and `haiti`. `Hashcat` also keeps a nice list hash types [here](https://hashcat.net/wiki/doku.php?id=example_hashes).

---

### Crack the hash: 48bb6e862e54f2a795ffc4e541caed4d

We can find the `hash type` with a few different tools like `hashid` or `haiti`.

![[crackthehash_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_01.png)

In this case it's `MD5`. Again, the more you see these, the quicker you'll get at identifying the type most likely in use.

We will try to be specific with the format by checking `john the ripper` formats.

![[crackthehash_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_02.png)

Place the hash in a file.

![[crackthehash_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_03.png)

Crack the first hash using `john the ripper` and using `--format=Raw-MD5` for the type.

![[crackthehash_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_04.png)

**Hash =** `easy`

---

### Crack the hash: CBFDAC6008F9CAB4083784CBD1874F76618D2A97

Find the hash type with `hashid`.

![[crackthehash_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_05.png)

Crack it with the format `Raw-SHA1`.

![[crackthehash_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_06.png)

**Hash =** `password123`

---

### Crack the hash:  1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032

Find the hash type.

![[crackthehash_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_07.png)

Set the hash to a file.

![[crackthehash_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_08.png)

Crack the hash with the format `Raw-SHA256`.

![[crackthehash_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_09.png)

**Hash =** `letmein`

---

### Crack the hash: \$2y\$12\$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

I'll assume that you're placing the hashes in files from this point forward and that you've used `hashid`/`hashcat -h` and `--list=formats` (if using `john`) or used Google, `hashid` or `haiti` (or others) to find the hash type.

![[crackthehash_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_10.png)

The hash will be cracked almost instantly.

![[crackthehash_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_11.png)

**Hash =** `bleh`

---

### Crack the hash: 279412f945939ba78ce0758d3fd83daa

Aside from using the above methods, you can also use an online tool such as `crackstation`.

![[crackthehash_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_12.png)

**Hash =** `Eternity22`

---

### Crack the hash F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85

Sometimes the output requires testing a few different types in order to be successful.

![[crackthehash_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_13.png)

Unfortunately, `hashid` is not 100% accurate in how it orders the hash types it guesses.

![[crackthehash_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_14.png)

**Hash =** `paule`

---

### Crack the hash: 1DFECA0C002AE40B8619ECF94819CC1B

Sometimes the type is actually pretty far down the list.

![[crackthehash_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_15.png)

You'll start to be able to guess the type from the list a bit more accurately as you become more familiar with hashes.

![[crackthehash_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_16.png)

**Hash =** `n63umy8lkf4i`

---

### Crack the hash: \$6\$aReallyHardSalt\$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.

Here the hash uses the salt value: `aReallyHardSalt`.

A hash starting with `$6$`, is `SHA-512`. The type typically used in `/etc/shadow`.

We can find what type of hash this is from Google or the example hashes page on `Hashcat's` [website](https://hashcat.net/wiki/doku.php?id=example_hashes) and searching the page for `$6$`.

![[crackthehash_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_17.png)

This will take a while.

![[crackthehash_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_18.png)

The password will be at the end of the output hash.

![[crackthehash_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_19.png)

**Hash =** `waka99`

---

# As an aside

If you want to speed up the processes for the sake of saving time because maybe you're not using your GPU (which will be much faster). You can split the `rockyou.txt` file to a new file to only include lines between, say: `2800000` and `2900000`... *hint, hint*.

If you wish to do this then you can use the following commands (no judgement, we're learning here and spending an hour or more waiting for your `VM` to finish churning through hashes wastes time and precious hamburgers):

- `sed -n '2800000,2900000p' /usr/share/wordlists/rockyou.txt > smaller_list`
- `hashcat -m 1800 hash8 smaller_list`

There are also methods that employ [Google Colab](https://github.com/someshkar/colabcat) to perform hash cracking. These are typically much faster as they use `GPUs` to crack the hashes.

---

### Crack the hash: e5d8870e5bdd26602cab8dbe07a942c8669e56d6

This is a `SHA-1` type hash but with a salt (salt: `tryhackme`). We can discover this by using `hashid` and reading the output. It won't tell us about the salt, however, we know we have one based on the task.

Set the hash file to read like this: `e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme`.

We can discover this by checking the `SHA-1` hash type on the `hashcat` examples page. It'll show us that `SHA-1` hashes with salts require a specific format (append at the end of the hash).

![[crackthehash_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_20.png)

The hash type for this is `HMAC-SHA1`. We want to use mode `160` (for `HMAC-SHA1 (key = $salt)`) on `Hashcat` for this one.

![[crackthehash_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_21.png)

![[crackthehash_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/CrackTheHash/images/crackthehash_22.png)

Once again the password is appended to the hash string in the output.

**Hash =** `481616481616`

