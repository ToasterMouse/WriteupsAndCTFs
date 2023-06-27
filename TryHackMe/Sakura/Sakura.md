![[sakura_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_00.png)

This was a really fun room and is up there as one of my favorite OSINT-based CTFs. 

---

# Task 1: Introduction

Are we ready to begin?

**Answer:** `Let's Go!`

---

# Task 2: Tip-Off

```
Task Introduction

The OSINT Dojo recently found themselves the victim of a cyber attack. It seems that there is no major damage,
and there does not appear to be any other significant indicators of compromise on any of our systems. However
during forensic analysis our admins found an image left behind by the cybercriminals. Perhaps it contains some
clues that could allow us to determine who the attackers were? 

Images can contain a treasure trove of information, both on the surface as well as embedded within the file
itself. You might find information such as when a photo was created, what software was used, author and
copyright information, as well as other metadata significant to an investigation. In order to answer the
following question, you will need to thoroughly analyze the image found by the OSINT Dojo administrators
in order to obtain basic information on the attacker.
```

We're given an image and asked to see if we can answer the one and only question: **What username does the attacker go by?**

![[sakura_01.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_01.png)

The company was attacked and what was left as proof is an image. If we look closer at the extension we can see that it's a `svg` (also known as `Scalable Vector Graphics`) file. The cool thing about them is they are clean, crisp and they have source code! This code includes meta-data.

![[sakura_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_02.png)

We can see the directory where the user held their image. From that we now have a username.

**Question:** `What username does the attacker go by?`<br />
**Answer:** `SakuraSnowAngelAiko`<br />

--- 


# Task 3: Reconnaissance


```
Task Introduction

It appears that our attacker made a fatal mistake in their operational security. They seem to have reused
their username across other social media platforms as well. This should make it far easier for us to gather
additional information on them by locating their other social media accounts. 

Most digital platforms have some sort of username field. Many people become attached to their usernames,
and may therefore use it across a number of platforms, making it easy to find other accounts owned by the
same person when the username is unique enough. This can be especially helpful on platforms such as on job
hunting sites where a user is more likely to provide real information about themselves, such as their full
name or location information. 

A quick search on a reputable search engine can help find matching usernames on other platforms, and there
are also a large number of specialty tools that exist for that very same purpose. Keep in mind, that sometimes
a platform will not show up in either the search engine results or in the specialized username searches due to
false negatives. In some cases you need to manually check the site yourself to be 100% positive if the account
exists or not. In order to answer the following questions, use the attacker's username found in Task 2 to
expand the OSINT investigation onto other platforms in order to gather additional identifying information on
the attacker. Be wary of any false positives!
```

For this section we are tasked with finding some more information on the attacker. We have a username and these may change but it's likely, also, that an attacker will be very comfortable with a particular handle and will use it in many places.

If we Google the name, one of the first hits we get is for a user on `github`.

![[sakura_03.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_03.png)

We can't be 100% sure that this is the same person (right at this moment) but, after doing some more recon on Google we can see that a person using derivations of this name can be found in multiple places. We can also confirm with a pretty good CI that we are looking at the same person by the name they have in common (among some other signals).

If we check the git we can see some repositories (that aren't forks) so we will look at those to see if we can uncover any information about the user and their email.

We will find a PGP key in one of them. 

![[sakura_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_04.png)

These are interesting as they can hold some user id data that can be used to identify the person who created the key. On the main page of the git we can see they use `Mailpile` which could be what they use with this public key (to encrypt and decrypt mail).

We can look at the meta data for these with `gpg`.

![[sakura_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_05.png)

We could also use a tool like [cirw gpg-decoder](https://cirw.in/gpg-decoder/) or import the key for more detailed information, but the meta should be easy to grab the way we did it.

**Question:** `What is the full email address used by the attacker?`<br />
**Answer:** `SakuraSnowAngel83@protonmail.com`<br />

Now, we need to user's full name to answer the next question. 

In our Google search from earlier we will have uncovered the user's Twitter account and on that they will have linked to another one of their accounts, which will display their full name.

![[sakura_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_06.png)

![[sakura_07.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_07.png)

Not as hidden as they'd like to be, I assume.

**Question:** `What is the attacker's full real name?`<br />
**Answer:** `Aiko Abe`<br />

---

# Task 4: Unveil

```
Task Introduction

It seems the cybercriminal is aware that we are on to them. As we were investigating into their Github
account we observed indicators that the account owner had already begun editing and deleting information
in order to throw us off their trail. It is likely that they were removing this information because it
contained some sort of data that would add to our investigation. Perhaps there is a way to retrieve the
original information that they provided?

On some platforms, the edited or removed content may be unrecoverable unless the page was cached or archived
on another platform. However, other platforms may possess built-in functionality to view the history of
edits, deletions, or insertions. When available this audit history allows investigators to locate information
that was once included, possibly by mistake or oversight, and then removed by the user. Such content is often
quite valuable in the course of an investigation. In order to answer the below questions, you will need to
perform a deeper dive into the attacker's Github account for any additional information that may have been
altered or removed. You will then utilize this information to trace some of the attacker's cryptocurrency
transactions.
```

We know that we found some repositories on the attacker's `github` that had to do with cryptocurrency. We found one called `ETH`, which stands for Ethereum.

**Question:** `What cryptocurrency does the attacker own a cryptocurrency wallet for?`<br />
**Answer:** `Ethereum`<br />

If we check the repository we'll find something interesting... a mining script. There doesn't seem to be much here, but, git commits are held within a log and we can check the history of commits through the history button.

![[sakura_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_08.png)

We will see that there have been two commits.

![[sakura_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_09.png)

Let's check the initial commit.

![[sakura_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_10.png)

There we will find a wallet address with their username and password.

**Question:** `What is the attacker's cryptocurrency wallet address?`<br />
**Answer:** `0xa102397dbeeBeFD8cD2F73A89122fCdB53abB6ef`<br />

We can check wallets and transactions using any `blockchain` explorer. Because we're looking at `Ethereum`, let's use `etherscan.io`.

To answer the next question we need to know what a mining pool is.

![[sakura_11.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_11.png)

OK, cool. Good to know. Now we should be able to understand what we're looking at when checking transactions.

![[sakura_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_12.png)

We will search by using the wallet address on `etherscan.io`. Now, we can see transactions, whether they're into or out of the wallet, the value, the age, the block and the mining pool they're from.

We're asked to find a specific transaction to find which mining pool the attacker transacted with on `Jan 23, 2021`. Click on `Age` to change to more human-readable dates.

![[sakura_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_13.png)

Nice! `Ethermine` was the mining pool.

**Question:** `What mining pool did the attacker receive payments from on January 23, 2021 UTC?`<br />
**Answer:** `Ethermine`<br />

The next one should be just as simple if we check through the transaction history,

![[sakura_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_14.png)

**Question:** `What other cryptocurrency did the attacker exchange with using their cryptocurrency wallet?`<br />
**Answer:** `Tether`<br />

---

# Task 5: Taunt


```
Task Introduction

Just as we thought, the cybercriminal is fully aware that we are gathering information about them after
their attack. They were even so brazen as to message the OSINT Dojo on Twitter and taunt us for our efforts.
The Twitter account which they used appears to use a different username than what we were previously
tracking, maybe there is some additional information we can locate to get an idea of where they are
heading to next?

Although many users share their username across different platforms, it isn't uncommon for users to also
have alternative accounts that they keep entirely separate, such as for investigations, trolling, or
just as a way to separate their personal and public lives. These alternative accounts might contain
information not seen in their other accounts, and should also be investigated thoroughly. In order to
answer the following questions, you will need to view the screenshot of the message sent by the attacker
to the OSINT Dojo on Twitter and use it to locate additional information on the attacker's Twitter
account. You will then need to follow the leads from the Twitter account to the Dark Web and other
platforms in order to discover additional information.
```

Apparently, the attacker noticed that we're onto them so they thought it was a good idea to start taunting us.

![[sakura_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_15.png)

Cool, well keep giving away your details. We're cool with that. We can now 100% confirm that we've been dealing with the right person. We need the user's twitter handle. It says `@AikoAbe3` at the top so, we'll grab the handle for their other account.

![[sakura_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_16.png)

**Question:** `What is the attacker's current Twitter handle?`<br />
**Answer:** `@SakuraLoverAiko`<br />

Now we're asked to find where her `wifi` stuff is being held. If we look through the twitter feed we found earlier we will find that she makes her private details, very public.

![[sakura_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_17.png)

They've given us a hash value, but, they mention they're holding their stuff on the dark web and that we'd have to look "DEEP" to where they've "PASTEd" them. Basically, they want to be caught. They're talking about `DeepPaste`.

![[sakura_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_18.png)

The onion link for deep paste should be easy to find with a little Googlin'.

Also, make sure to check all `Aiko's` posts. They mention that their link got removed and added an new `MD5` hash to access their stuff.

![[sakura_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_19.png)

You may have luck with the page loading the paste or not. It was not working for me, at first. I tried a few `.onion` links and got no joy. 

![[sakura_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_20.png)

![[sakura_21.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_21.png)

I also tried the `.onion` link in the image and got nothing. 

What image you say? If you have problems then you can grab an image from the hint that will have a screenshot of the deep paste bin in question. Some times this page will be up and other times it'll be down. I tried this room a few days later and the link was up.

![[sakura_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_22.png)


**Question:** `What is the URL for the location where the attacker saved their WiFi SSIDs and passwords?`<br />
**Answer:** `http://deepv2w7p33xa4pwxzwi2ps4j62gfxpyp44ezjbmpttxz3owlsp4ljid.onion/show.php?md5=b2b37b3c106eb3f86e2340a3050968e2`<br />


It's like she wants to be caught. Now we need to find the `BSSID` for their home `WiFi`. It's `SSID` (Service Set Identifier (Network Name)), is `DK1F-G` (which we will find in the deep-paste bin). We can search a repository of these identifiers at [WiGle](https://wigle.net/) (it will have been added there so we shouldn't have problems finding it). 

However, be careful, you only get a handful of requests you can make before you'll be locked out for a while.

This will happen!

![[sakura_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_23.png)

Before this happened to me, I searched the `SSID` and it didn't return anything. It does that sometimes even when there is actually something there.

There is another way we can do this. If we check their `DeepPaste` again we can see that above `Home WiFi` is an entry for `City Free WiFi` called `HIROSAKI_FREE_Wi-Fi`. From the twitter we can see they're talking about heading home, there is a satellite image of Japan and what looks like an airport lounge. So, they're home is in `Hirosaki, Japan`. Let's use the `WiGle` Map and try to do this manually.

The easiest part is zooming into `Hirosaki`. The smartest way to find the access point manually is to move to a cluster of dots and then zoom in and look closer. If nothing is there, zoom out, find another cluster and repeat.

![[sakura_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_24.png)

![[sakura_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_25.png)

This might take a while! Once we find it, we'll click the dot to get more information.

![[sakura_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_26.png)

You dun goofed! Fully back-traced.

**Question:** `What is the BSSID for the attacker's Home WiFi?`<br />
**Answer:** `84:AF:EC:34:FC:F8`<br />

---

# Task 6: Homebound


```
Task Introduction

Based on their tweets, it appears our cybercriminal is indeed heading home as they claimed. Their Twitter
account seems to have plenty of photos which should allow us to piece together their route back home. If
we follow the trail of breadcrumbs they left behind, we should be able to track their movements from one
location to the next back all the way to their final destination. Once we can identify their final stops,
we can identify which law enforcement organization we should forward our findings to.

In OSINT, there is oftentimes no "smoking gun" that points to a clear and definitive answer. Instead, an
OSINT analyst must learn to synthesize multiple pieces of intelligence in order to make a conclusion of
what is likely, unlikely, or possible. By leveraging all available data, an analyst can make more informed
decisions and perhaps even minimize the size of data gaps. In order to answer the following questions, use
the information collected from the attacker's Twitter account, as well as information obtained from
previous parts of the investigation to track the attacker back to the place they call home.
```

The first question will guide us forward. We are asked to find the "airport closest to the location the attacker shared a photo from prior to getting on their flight?"

Not a prolific tweeter, but, everything they tweet is a gold mine for us.

![[sakura_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_27.png)

Where is that? We know they're heading home to Japan. But, this looks more like somewhere in the USA. Another user who the attacker retweeted, leaks the potential location. 

![[sakura_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_28.png)

Bethesda is in Maryland, USA and there are `Sakura` tree attractions there.

![[sakura_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_29.png)

`Kenwood` is right next to Bethesda. But, it's not as close to the water as what it looks like in the image.

![[sakura_30.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_30.png)

Let's see where `Aiko` was exactly. Because we're prolific photographers and social media addicts in this day and age, finding the exact location should be pretty easy.

![[sakura_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_27.png)

In the image on `Aiko's` twitter, we can see what looks like a raised walkway in front, the Washington Monument in the background (front), the Capitol Building off to the right (just the roof), what looks like train tracks running along the right-hand of the path, the path contains trees running down the center with some concrete slabs between them, a red-brick building on the left and just ahead of that what looks to be soccer fields. To the left of the fields we can see what appears to be highway/freeway signage and lastly, the image was taken alongside what looks to be a river or lake. 

![[sakura_31.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_31.png)

That's probably enough, although there is more we can take into account to make sure we get the right location. There's a lot to go on here. Let's do it.

After a little looking around I found the location where the photo was taken. Outside the Crystal City Lofts, Arlington, Virginia. Unfortunately, there is no street view at this specific location but just ahead there are some pictures that have been uploaded that confirm we're in the right spot.

![[sakura_32.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_32.png)<br />
*The satellite images are from 2023*

**Angle toward the Washington Monument - facing ~68-70 degrees, North-West**<br />
![[sakura_33.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_33.png)

**Backward, towards the lofts**<br />
![[sakura_34.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_34.png)

**Forward, towards the Washington Monument**<br />
![[sakura_35.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_35.png)

**A little further back - closer to picture's position**<br />
![[sakura_36.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_36.png)

**Approximately the same position as above, but the other side of the walkway - Road signage is clearly visible from here as well as the soccer/hockey fields**<br />
![[sakura_37.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_37.png)

We're definitely in the right spot!

Now let's find out what airport is close by. 

![[sakura_38.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_38.png)

Google is so neat!

**Question:** `What airport is closet to the location the attacker shared a photo from prior to getting on their flight?`<br />
**Answer:**  `DCA`<br />

Now, we need another airport. The one that `Aiko` had her layover in.

![[sakura_39.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_39.png)

`JAL` is Japan Airlines. The `Sakura` Lounge is something we can use to find out which airport she was in when the picture was taken.

![[sakura_40.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_40.png)

It looks like she was in `Haneda` airport. Google will help us find the `Haneda` airport code.

![[sakura_41.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_41.png)

**Question:** `What airport did the attacker have their last layover in?`<br />
**Answer:** `HND`<br />

The next question asks us about a lake in the satellite image we found on `Aiko's` twitter. That should be easy to figure out.

I've pointed to some obvious landmarks that make narrowing down the lake quick and easy.

![[sakura_42.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_42.png)

Below, we can see them again but now with place names. `Sado Island` (on the left), the tapering of the land around `Tainai` and `Natori`. Lastly, the Lake outside of `Fukushima`.

![[sakura_43.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_43.png)


`Lake Inawashiro`<br />
![[sakura_44.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Sakura/images/sakura_44.png)

**Question:** `What lake can be seen in the map shared by the attacker as they were on their final flight home?`<br />
**Answer:** `Lake Inawashiro`<br />

Finally, we have the answer to the last question already. Remember the free city wifi SSID we found on `DeepPaste`?

**Question:** `What city does the attacker likely consider "home"?`<br />
**Answer:** `Hirosaki`
