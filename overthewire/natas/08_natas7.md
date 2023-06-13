![natas7_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas7_01.png)

Heading out to the next level we'll see something new.

![natas7_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas7_02.png)

We have two links. What happens when we follow them?

Home.

![natas7_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas7_04.png)

About.

![natas7_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas7_05.png)

Not much. Let's check the source code.

![natas7_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas7_03.png)

It tell us where the password is but how do we access it?

Take a look at the URL bar.

![natas7_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas7_06.png)

Note `index.php?page=about`. This is a potential vector for what's called Local File Inclusion (LFI). With LFI it can be possible to read files on the underlying server by abusing parameters such as `page` due to the use of vulnerable `php` functions like `include()`, `include_one()`, `require()` or `require_once()`.

Let me introduce you to [HackTricks](https://book.hacktricks.xyz/pentesting-web/file-inclusion), specifically their entry on LFI. If we read through the page we'll come across the top 25 potentially vulnerable endpoints for `php`.

![natas7_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas7_07.png)

As you can see, `page` is included in this list. 

We know of a file we would like to read and there is a decent chance of the site being vulnerable to LFI. Let's see if we can use this `page` parameter to access the file.

Payloads can get a bit more complicated but for this level all that needs to be done is to specify the path in the URL bar.
![natas7_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas7_08.png)

There's the password!

![natas7_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas7_09.png)

---

# The Python Way

Here's how we can perform the exploit for this level with `python`.

![natas7_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas7_10.png)
