![bandit32_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_01.png)

No more `git` but another jailbreak. The "Good luck" seems a little ominous. Let's check out the machine to see what we get.

First, let's do a little recon on what shell `bandit32` is using by checking `/etc/passwd` from `bandit31`.

![bandit32_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_02.png)

It's an `SUID` binary called `uppershell`.

![bandit32_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_03.png)

It's a "setuid regular file". That gives me an idea.

First, let's play around with the shell for a bit.

![bandit32_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_04.png)

Aside from the misspelling of `whoami`, we can see that no command seems to be working but we know that we're essentially in a shell handled by a binary process that is owned by `bandit33` - it will become clearer why this is important in a bit.

Now let's have a closer look at the error messages. Error messages are super important and can leak very useful things to us, even if they seem to be innocuous at first.

![bandit32_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_04.png)

The error says that `sh`, that is `/bin/sh` for a command shell, cannot find any of the commands we gave it. The issue here is that it looks like the binary is using `sh` to execute commands, however these commands seem to be converted to uppercase first, which would make them invalid. This makes me think that the binary is probably using C's `system` function for the commands we pass this "shell". If this is the case, with a little trickery, we should be able to spawn a new shell that will drop us outside this binary process as the `bandit33` user.

Let's test a little more and see if a simple call to `/bin/sh` will work.

![bandit32_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_05.png)

Unsurprisingly, that doesn't work. Our commands always get converted to uppercase it would seem. Thats ok, we don't give up around here.

We know that our commands are being run with `sh`, so let's consider for a second how the command interpreter parses arguments.

When we write a bash script, how are arguments given? You may have to read up on this but you can press the "I believe" button for now. They are accessed in scripts by the bash argument variables `$1, $2, .. $n`. 

So, if a script expects an argument to do something like this:

![bandit32_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_06.png)

The script may look something like this:

![bandit32_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_07.png)

You may know that when it comes to programming, things are typically zero-indexed. That means, lists/arrays of things start from zero. It's the same for shell scripts. The zeroth argument variable for a script is actually the script itself.

So, what if we do this?

![bandit32_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_09.png)

Did you expect this to occur?

![bandit32_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_08.png)

What if we pass it `$0`?

![bandit32_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_10.png)

Oh! It will return the shell I am running. Here, I am on `Kali Linux` and the version I am currently running uses `zsh` as opposed to `bash`, by default.

So, what happens if we send the `$0` argument variable to `sh` in `uppershell`?

![bandit32_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_11.png)

We spawn a new shell process and escape the `uppershell` jail. It's a neat little trick where we get `sh` to basically call itself!

![bandit32_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_12.png)

Notice that the `ps` command shows that we are running two shells!

![bandit32_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_13.png)

We can see in a more detailed read out (using `ps auxf`) that the `uppershell` was running `sh -c $0` which spawned our new shell (parsed as `sh -c sh`). Which subsequently ran the call to `ps`.

Now we can grab the password and move forward.

![bandit32_14.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_14.png)

On to the... nope, that's all the `bandit` levels complete! Well done! GG!

---

# Addendum

This is a little beyond the scope of the last challenge, however, let's figure out exactly how this level works by downloading the `uppercase` binary from the machine (using `scp`) and checking out the source code in `ghidra`.

![bandit32_15.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit32_15.png)

*I renamed the important variables to make it a little bit easier to understand*

It's a pretty simple shell `jail`. It runs eternally with a `while true` loop. It will grab the command that we pass it and then iterate through the command letter by letter. Next, it'll convert each letter to uppercase and finally, it'll run the command using `system`. 

The `system` function will allow you to perform command line calls, for example:

```bash
# system will perform ls -lah and return the directory listing to stdout
system('ls -lah');
```

However, the uppercase version (`LS -LAH`) is not a supported command as the command line is case sensitive. This is why our common commands are not working. However, because there is no uppercase version of `$` or `0`, they return exactly as they are: `$0`. As all shell scripts are run with a shell interpreter like `sh`, passing `$0` is the same as passing `sh` and that is how it'll be interpreted by `system`. In this way, we can bypass the code intended to sanitize our input. 

Pretty cool, huh?

