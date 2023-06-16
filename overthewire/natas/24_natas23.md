![natas23_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas23_01.png)

A password input!

![natas23_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas23_02.png)

Let's check the source, once again!

![natas23_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas23_03.png)

If `passwd` is in the request and it's longer than 10 characters than it'll give us the credentials.

`strstr()` will check if a string is within a string. So, as long as `iloveyou` exists in our password, we'll beat the login.

The password `iloveyou` is 8 characters. That means we just need to prefix or append two other characters.

Let's try this password: `14iloveyou`.

![natas23_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas23_04.png)

Send it to the moon!

![natas23_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas23_05.png)

---

# The Python Way

![natas23_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas23_06.png)

This is super simple, just send the data over making sure that the key is `passwd` and our password is `10` characters or more.

![natas23_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas23_07.png)

