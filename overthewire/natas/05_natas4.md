![natas4_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas4_01.png)

This time we have to be visiting from a specific address to access the page. At least, the server must think that we are doing that.

![natas4_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas4_02.png)

We can do this through the web request headers in the `DevTools Network` tab, or we could start to play with the `Python` requests module.

Let's use `Python`. Here's a script to access to page and grab the password.

![natas4_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas4_03.png)

The above code may seem a little daunting at first if you're unfamiliar with the requests module but, let's walk through it line by line.

At the top we import the modules we want to use.

```python
import requests
import re
```

The `requests` module allows us to make web requests while the `re` module allows us to do `regex` pattern matching on the returned content.

We will set the `username` and `password` which `requests` can use with the `auth` parameter to login to each page (using basic/digest authentication).

```python
username = 'natas4'
password = 'tKOcJIbzM4lTs8hbCmzn5Zr4434fGZQm'
```

We can also specify headers which we can send using the `headers` parameter. Here we're defining a dictionary list to define the new header. 

```python
headers = { 'Referer' : 'http://natas5.natas.labs.overthewire.org/'}
```

The `Referer` header is used for the server to be able to identify from where a request is made. As an aside, the misspelling of the word is preserved as more of a tradition as it was introduced as "Referer" in the original proposal for the header by Phillip Hallam-Baker and set in stone by circa 1996.

Now, we set the `url` we wish to access with our python `request` and use the python format string syntax to add the username for the subdomain instead of hard-coding it.

```python
url = f'http://{username}.natas.labs.overthewire.org/'
```

Then, we make a `GET` request to the `url` with the credentials (passed as a tuple to `auth`) and the header (passed as a dictionary list to `headers`).

```python
response = requests.get(url, auth = (username,password), headers = headers)
```

We then set the content of the response to a new variable.

```python
content = response.text
```

Finally, we will use `regex` to grab the password.

```python
print(re.findall('natas5 is (.*)',content)[0])
```

If we make the request (in sublime-text we can do that by building the script with `ctrl+b`) without the `regex` and simply `print(content)` we can see that the request was successful and the page's source is sent back to us.

![natas4_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas4_04.png)

The `regex` call simply grabs the line where the password is and carves the password out which we subsequently print to the screen.

![natas4_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas4_05.png)
