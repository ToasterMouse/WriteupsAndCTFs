![bandit10_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit10_01.png)

Now we move onto something that we see all the time in CTFs. Base64.

Base64 is a common easily reversible encoding format that looks something like this:

```
SGFja2luZ0lzRnVuCg==
```

A way to be able to figure out if some text is encoded into `base64` is by seeing trailing `=` signs. Although, this isn't a 100% accurate method. However, as time progresses you get used to seeing different encoding/encryption formats that ones like `base64` become easy to spot.

Also, note I mentioned that it's easily reversible. That's because it's encoding, not encryption (encoding is far less secure than encryption). We can find out what the value is in plaintext by using the `base64` utility with it's `-d` (decode) switch.

We will find the `data.txt` file as soon as we log in and it contains `base64` encoded data, as we expected.

![bandit10_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit10_02.png)

Just like other tools, we can `cat` this file and pipe the output to the `base64` utility.

![bandit10_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit10_03.png)

Nice! On to the next level: `level10 -> level11`.

