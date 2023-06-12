![natas3_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas3_01.png)

There's nothing here, again!

![natas3_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas3_02.png)

What about the source code!?

![natas3_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas3_03.png)

The hint here is that `not even Google will find it`. What does Google find when we talk about web pages? Well, it crawls websites and indexes their content for their search engine. However, there may be pages on our site that we might not want Google to access, such as an `admin` portal.

In order to prevent Google from crawling certain pages, a site can use the `robots.txt` file, which is a set of rules for Google crawlers to follow. This file can `disallow` certain pages from being crawled. However, it should be noted, this is not a method to keep a web page out of Google, which must be done by blocking indexing with `noindex` or password protecting the page.

For more on Google crawling and indexing check [here](https://developers.google.com/search/docs/crawling-indexing).

So, just as the message says on the page we're on there isn't anything on that page. It didn't lie. However, a common web enumeration check that should always be performed is to look for `robots.txt`. In fact, many scanners will look for this file when using default scanning switches.

![natas3_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas3_04.png)

This simple check can sometimes uncover directories that a developer might want to keep "secret".

![natas3_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas3_05.png)

Another directory with listing enabled.

![natas3_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas3_06.png)

Nom nom. Another password.
