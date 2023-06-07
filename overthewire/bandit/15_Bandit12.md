![bandit12_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit12_01.png)

Here we will need to do a little analysis of a `hexdump` which is of a file that has been compressed a bunch of times. This is a classic Matryoshka challenge. Like the dolls, a file is inside a file which is inside a file, and so on.

![bandit12_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit12_02.png)

The file is an ASCII text file that contains a hexdump, as promised.

We can work with hexdumps by using the `xxd` utility.

![bandit12_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit12_04.png)

As we can see when we check the manual, `xxd` can make a hexdump or convert a hexdump to a binary file, among other things. 

![bandit12_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit12_03.png)

If we check the help we can find that we need to use the `-r` switch to convert our data to a binary file.

We'll start by creating a new directory in `/tmp` to work with the file as the instructions suggest.

![bandit12_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit12_05.png)

Then we'll convert the hexdump with `xxd -r <hexdump> <new_filename>`.

![bandit12_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit12_06.png)

Now we have our `new_data` file. We can use the `file` utility to find out what type of binary we're looking at.

![bandit12_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit12_07.png)

We can see that the `new_data` file is a `gzip` archive. 

First, we will rename the file to use the `.gz` extension and then extract the archive with `gunzip` (we can also use the `gzip` utility for this).

![bandit12_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit12_08.png)

The curly braces are a way to easy change a file extension. It needs `{text to replace, text to replace with}`. When we leave the first entry empty the data will be appended to what we're trying to change.

Now we can see that a new file replaces the file we unzipped.

![bandit12_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit12_09.png)

This time it's a `bzip2` file. We can unzip these with `bzip2` or `bunzip2`. We can follow the same method as we did before by renaming the file, extracting the archive and then seeing what new file gets created.

We can use this method to drill down into the file to finally find the password.

![bandit12_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit12_10.png)

The steps I took to grab the password file are as follows:

```
$ xxd -r data.txt new_data
$ file new_data                                                                  
new_data: gzip compressed data, was "data2.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 581
$ mv new_data{,.gz}
$ gunzip new_data.gz

$ file new_data
new_data: bzip2 compressed data, block size = 900k
$ mv new_data{,.bz2}
$ bunzip2 new_data.bz2

$ file new_data 
new_data: gzip compressed data, was "data4.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 20480
$ mv new_data{,.gz}
$ gunzip new_data.gz

$ file new_data 
new_data: POSIX tar archive (GNU)
$ mv new_data{,.tar}
$ tar xvf new_data.tar
data5.bin

$ file data5.bin
data5.bin: POSIX tar archive (GNU)
$ mv data5{.bin,.tar}
$ tar xvf data5.tar
data6.bin

$ file data6.bin
data6.bin: POSIX tar archive (GNU)
$ mv data6{.bin,.tar}
$ tar xvf data6.tar
data8.bin

$ file data8.bin 
data8.bin: gzip compressed data, was "data9.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 49
$ mv data8{.bin,.gz}
$ gunzip data8.gz

$ file data8
data8: ASCII text
$ cat data8
The password is wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw
```

On to the next level: `level13 -> level14`.
