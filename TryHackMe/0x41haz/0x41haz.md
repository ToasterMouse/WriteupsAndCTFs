![[0x41haz_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_00.png)

This is a simple crack me that will show you one way that a program will read data in memory.

---

First, download the file called `0x41haz.0x41haz` and if you use `file` on the program you'll notice something interesting.

It's not recognized.

![[0x41haz_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_01.png)

If we check the magic number we might not notice the problem straight away.

But, if we see a properly formatted ELF binary, we'll notice something is off.

![[0x41haz_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_02.png)

We can see that `bash` has the file signature of `7F 45 4C 46 02 01`. Our's is `7F 45 4C 46 02 02`.

![[0x41haz_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_03.png)

That means, if we change `02 02` to `02 01`. We will fix the file and have our proper file signature.

Now we can run the program.

![[0x41haz_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_04.png)

It wants a password. OK, let's see if we can find one either hard-coded or see if there is some program functionality we can reverse engineer. I'll load the binary into `ghidra`.

![[0x41haz_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_05.png)

When we open it in `ghidra` we will find our entry point and have to double click on the function that is pointed to in the image above to get to our main function (image below).

![[0x41haz_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_06.png)

From top to bottom we can see the variables that are used, some hard-coded hexadecimal, a conditional that is using the hexadecimal for the number `13` to check the password length and a conditional to check if the characters we input are the same as what is hard-coded.

`local_1e` is only 8 characters long, but if we add `local_16` and `local_12` we get a string of characters that is `13` characters long.

If we convert the characters, we get:

```
fg$52@@2@&TsL
```

Let's see if we can now beat the binary.

![[0x41haz_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_07.png)

Nope? huh!?

Let's check the hardcoded values again.

![[0x41haz_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_08.png)

It's hexadecimal, not a char array, or a pointer.

Let's check the endianness of the file.

![[0x41haz_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_09.png)

It's little-endian. That means that the hex will be read from right to left.

![[0x41haz_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_10.png)

The `if` conditional is iterating from `local_1e + i`, `local_1e` is only 8 characters long and the loop will end when `12` (`0xc`) is less than the iterator (`local_c`). Picking apart this function can take a little getting used to as the code here is `ghidra's` best guess and so can get pretty confusing at times. 

From the way the code is presented we can tell that the program is going to read into memory locations beyond `local_1e` and into whatever is next in memory. This should be `local_16` and then `local_12`.

Meaning, if we break down the variables and their values (remember to read the hex backwards to account for it being in little-endian), the password will be:

```
local_1e = 2@@25$gf
local_16 = sT&@
local_12 = L

password = 2@@25$gfsT&@L
```

Let's test to see if it works.

![[0x41haz_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_11.png)

Now we just need to add `THM{}` and that's the flag: `THM{2@@25$gfsT&@L}`.

---

# Take it one step at a time (proof)

If you want to see this in action, we can open the binary in `ida`.

First, we'll find where the comparison of the password and our input is performed. This is super easy with the graph view because we can follow the code branches (from conditionals) and find the exact point of comparison very quickly (especially for such a small program).

![[0x41haz_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_12.png)

Set a breakpoint on the line where the comparison is done, run the program (it'll run in the terminal) and paste in the password.

![[0x41haz_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_13.png)

Now we can step through the program and keep an eye on the `dl` and `al` values.

![[0x41haz_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_14.png)

We can see in the stack view, our input and the hard-coded (the bottom one) password.

To see it clearer, we can just pass zeroes.

![[0x41haz_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_15.png)

![[0x41haz_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_16.png)

Because `dl` is the lower section (last byte) of the `EDX` (bottom half of the `RDX`) registry, we can also discover the password by stepping through each comparison with `IDA` and keeping an eye on the `RDX` register.

![[0x41haz_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_17.png)

On this iteration `dl` is `0x32` (that's `2` in decimal).

This way, we can discover what each character of the password is supposed to be just by stepping through a loop. We didn't have to do that this time but it's good to know if we need to do that.

You might ask: It's hard-coded, isn't there an easier way?

Yup! We can also break at where the `EDX` registry gets populated and hover over the byte pointer to see a fair chunk of the values and the location on the stack.

![[0x41haz_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/0x41haz/images/0x41haz_18.png)

We only get 10 values but we can line that up with the memory address `0x7FFCDD3B329A` (top right of popup - where the values are stored). We can right click in the stack to follow a memory address in the hex dump where we will also be able to see the password in clear text.

`IDA` is super powerful but, as you can see, with a little knowledge about endianness, we can quickly figure out what we need to do without it!
