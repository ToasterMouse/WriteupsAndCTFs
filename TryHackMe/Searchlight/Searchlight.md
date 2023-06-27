![[searchlight_00.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_00.png)

This room is a little different from most of the others that we've done so far. 
Today we're looking at `IMINT` (Image Intelligence). A form of `OSINT` (Open Source Intelligence).

---

For each task we'll be given an image and asked to answer some questions about them. A bit of general knowledge will go a long way. However, I'll show methods to discover these things without prior knowledge. 

There are 5 elements of `IMINT` that you should consider when looking at an image:

1. Context
2. Foreground
3. Background
4. Map markings
5. Trial and error

There are also quite a few basic things to look for in an image. These things you will be familiar with if you've ever played [GeoGuessr](https://www.geoguessr.com/). 

- Landmarks
- Street signs
- Location notation on items
- Billboards/Advertisements
- Menus
- Business names/Building numbers
- Direction of sunlight/shadows/satellite dishes and so on

Here are some good resources to know about before we begin:

- [Google](www.google.com)
- [A lesson on looking | Amy Herman](https://www.youtube.com/watch?v=_jHmjs2270A)
- [Use FFMPEG for video stills](https://nixintel.info/osint-tools/using-ffmpeg-to-grab-stills-and-audio-for-osint/)
- [Guide To Using Reverse Image Search](https://www.bellingcat.com/resources/how-tos/2019/12/26/guide-to-using-reverse-image-search-for-investigations/)
- [Tips and Tricks on Reverse Image Searches](https://osintcurio.us/2020/04/12/tips-and-tricks-on-reverse-image-searches/)

Let's begin. For the answers, we'll have to use this format: `sl{<answer>}`

---

# Your First Challenge!

We'll start off with a very simple task. The only tool we'll be using is: our eyes.

**Question:** `What is the name of the street where this image was taken?`

![[searchlight_01.jpg]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_01.jpg)

We can see it in a few places, the most obvious is right in the center

![[searchlight_02.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_02.png)

**Answer:** `sl{Carnaby Street}`

---

# Just Google It!

This should be pretty simple. We can use some basic `Google Dorking` techniques to zero in on our answer.

**Question:** `Which city is the tube station located in?`

![[searchlight_03.jpg]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_03.jpg)

This may seem very obvious to some people. However, if we don't know how could we find out? In fact, for this task when we find this out, we'll find out the answers to ALL our questions for this section.

There are a few ways. The simplest is to look for any names that stand out. We could grab an image of the `Underground` logo and use image search to see what matches. Or, we could find a street name, like this one:

![[searchlight_04.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_04.png)

We can now Google a few things to see what we can find, something like this:

- `the tube circus station`

![[searchlight_05.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_05.png)

Here we can see the same location, the name is visible and we can see the same GAP and billboard facade to the right as well as the buildings behind the entrance.

Now we can answer the questions by searching the location `Piccadilly Circus`.

![[searchlight_06.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_06.png)

**Question:** `Which city is the tube station located in?`<br />
**Answer:** `sl{London}`<br />

**Question:** `Which tube station do these stairs lead to?`<br />
**Answer:** `sl{Piccadilly Circus}`<br />

**Question:** `Which year did this station open?`<br />
**Answer:** `sl{1906}`<br />

**Question:** `How many platforms are there in this station?`<br />
**Answer:** `sl{4}`<br />

---

# Keep at it!

![[searchlight_07.jpg]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_07.jpg)

This one may seem a little more difficult but there's information right in our faces. The billboard in the background.

![[searchlight_08.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_08.png)

`YVR`. What is that?

The word "connects" will clue us in and help us understand what we're looking at. If you have a little knowledge about airports you know that they are all given shorthand handles. `YVR`, is one of these. If we didn't know this, we could just either visit the website on the board or just Google `YVR`.

`.ca` is the `TLD` for `Canada` so we're going to be looking at a Canadian airport. `VR`, is Vancouver.

![[searchlight_09.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_09.png)

If we visit the website we'll see that it is the Vancouver International Airport and if we check the Contact Us details on the site we'll see their mailing address.

![[searchlight_10.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_10.png)

We can see that the terminal is in Richmond, British Columbia.

**Question:** `Which building is this photo taken in?`<br />
**Answer:** `sl{Vancouver International Airport}`<br />

**Question:** `Which country is this building located in?`<br />
**Answer:** `sl{Canada}`<br />

**Question:** `Which city is this building located in?`<br />
**Answer:** `sl{Richmond}`<br />

---

# Coffee and a light lunch

![[searchlight_11.jpg]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_11.jpg)

Once more we'll let the questions guide us. We first want to know where this coffee shop is located. We have a big hint just outside the window `The Edinburgh Woollen Mill`.

![[searchlight_12.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_12.png)

Well, there ARE a lot of them. We might have some luck if we check out the images associated with these locations or we can check out `street view`. Because the name mentions `Edinburgh` that biases me to check Scotland first.

Checking the images we'll find a match in `Blairgowrie`.

![[searchlight_13.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_13.png)

Notice the stairs that lead up to the entrance? That's interesting as it matches the photo we were given. We'll also notice something even more interesting. A match for the exact photo.

![[searchlight_14.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_14.png)

So, it's in `Blairgowrie`. What street is it on? Because we know that the coffee shop is across the street from the store, we'll easily find the street it's on: `Allan Street`

![[searchlight_15.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_15.png)

Now we want their phone number. That should be pretty easy too. We now know the Coffee Shop's name: `The Wee Coffee Shop`.

![[searchlight_16.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_16.png)

Now let's look for their email. A real easy place to find these is `Facebook`, let's check to see if they have a page.

![[searchlight_17.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_17.png)

Lastly, we just need to owners surnames. This is simple too. There are MANY ways to grab this but let's just Google it.

We'll find a website all about `Blairgowrie` so this should be pretty accurate, if we simply search `The Wee Coffee Shop Owners` we will find what we're looking for. Stand aside U2.

![[searchlight_18.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_18.png)

If we want more confirmation we can also read through the Facebook page and get a confirmation on their first names (at least).

![[searchlight_19.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_19.png)

Also, I'm very hungry now, the food looks great!

![[searchlight_20.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_20.png)

**Question:** `Which city is this coffee shop located in?`<br />
**Answer:** `sl{Blairgowrie}`<br />

**Question:** `Which street is this coffee shop located in?`<br />
**Answer:** `sl{Allan Street}`<br />

**Question:** `What is their phone number?`<br />
**Answer:** `sl{+447878 839128}`<br />

**Question:** `What is their email address?`<br />
**Answer:** `sl{theweecoffeeshop@aol.com}`<br />

**Question:** `What is the surname of the owners?`<br />
**Answer:** `sl{Cochrane}`<br />

---

# Reverse your thinking
Now we're going to get used to performing some reverse image searching. Things are spicing up!

![[searchlight_21.jpg]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_21.jpg)

Now this is interesting. There's not much to go on. So we'll use Google Image Search to see if it can find anything. I am not going to use `RevEye` or `TinEye` as these will bring back hits for other write-ups for the Searchlight room (I almost got spoiled!).

First, we're asked to find out what restaurant we're looking at. It's very busy so we can assume a few things, especially with the decor. Perhaps it's in New York?

You'll find out that assumptions will serve you well.

![[searchlight_22.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_22.png)

Nice, we have the name of the deli. Now we are asked an interesting question about an editor for `Bon Appétit` that worked a `24 hour shift at Kats'z Deli`. This should be easy to find.

![[searchlight_23.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_23.png)

The first hit we get on Google is the article in question.

![[searchlight_24.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_24.png)

A nice little introduction to reverse image searching.

**Question:** `Which restaurant was this picture taken at?`<br />
**Answer:** `sl{Katz's Deli}`<br />

**Question:** `What is the name of the Bon Appétit editor that workd 24 hours at this restaurant?`<br />
**Answer:** `sl{Andrew Knowlton}`<br />

---

# Locate this sculpture

Now we have a sculpture to find. `IMINT/OSINT` is really fun, right?

![[searchlight_25.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_25.png)

I like that sculpture. 

Taking a quick guess, it looks like this is in Europe, somewhere. It doesn't really narrow it down but it's a baseline.

Let's use some more image searching to see if we can find out WHERE this sculpture actually is located.

![[searchlight_26.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_26.png)

We can find two image sites that mention the name AND where it's located. Rudolph the Chrome Nosed Reindeer in Oslo. 

Let's also use `Bing` image search to see if we get anything else.

![[searchlight_27.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_27.png)

We'll find some information that whittles down the location of the sculpture! Nice.

If we Google the name and the city, we'll find a tourist website `visitoslo`.

![[searchlight_28.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_28.png)

When we visit the site, we'll be taken to an article about the sculptures that surround the city and at the bottom of this page there is map with all their locations.

If we look for `Tjuvholmen`, we'll find the sculpture, the picture we were given AND the name of the person who took it. You may have found `Apparatijk` on `Alamy` but the images there are not the same as the one we were given. It seems like this image is solely used on `visitoslo.com`.

![[searchlight_29.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_29.png)

This one was fun. It was a little more involved and required a little more digging for answers. The interactive map was a nice touch! 

**Question:** `What is the name of this statue?`<br />
**Answer:** `sl{Rudolph the Chrome Nosed Reindeer}`<br />

**Question:** `Who took this image?`<br />
**Answer:** `sl{Kjersti Stensrud}`<br />

---

# ...and Justice For All

![[searchlight_30.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_30.png)

With this one we are warned that it'll be a step up in difficulty so let's prepare ourselves.

We're given an image of `Lady Justice` and asked some questions. The first one I just answered. A little bit of prior knowledge helps. However, you could find the name of this statue with a quick google search.

Now we want to know where this statue is located. 

Image search to the rescue... again!

![[searchlight_31.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_31.png)

We can see the actual image that is from `theverge`. But, what can be useful, is alternate images of the same thing. So we'll look at a few others from different sources.

If we check the third link from the left on the top row we'll be met with a site that exposes where the statue is located.

![[searchlight_32.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_32.png)

![[searchlight_33.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_33.png)

Nice! However, another method to discover it could be to just using `Bing` or `Yandex`, etc. Or, if we check `theverge's`  version of the image we can see it's tagline `Justice blind court statue inline (STOCK)`. This can ALSO be used to find out information about the location. 

![[searchlight_34.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_34.png)

Stock images can give you a lot of information!

![[searchlight_35.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_35.png)

Now we have multiple images we can check out.

![[searchlight_36.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_36.png)

There are many ways to get to the goal. Now we know the statue is outside the Albert V. Bryan United States Courthouse in Alexandria, Virginia.

Now we just need to find the name of the building that sits across from the courthouse.

![[searchlight_37.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_37.png)

And that's it!

**Question:** `What is the name of the character that the statue depicts?`<br />
**Answer:** `sl{Lady Justice}`<br />

**Question:** `Where is this statue located?`<br />
**Answer:** `sl{Alexandria, Virginia}`<br />

**Question:** `What is the name of the building opposite from this statue?`<br />
**Answer:** `sl{The Westin Alexandria Old Town}`<br />

---
# The view from my hotel room

This is a really fun one. Probably because I like `GeoGuessr` and this one requires a little Google Map walking. 

The information for this task mentions that you can use `FFmpeg` to grab all the stills from the video and do reverse image searches based on that and that IS possible. However, we don't need to do that.

If you'd like to follow that method you can read more about `FFmpeg` [here](https://nixintel.info/osint-tools/using-ffmpeg-to-grab-stills-and-audio-for-osint/).

We'll watch the video and I'll point out four things that really stood out to me.

![[searchlight_38.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_38.png)

There are a few other landmarks that will be useful but, these are the top four that give the place away. You'll also notice some large umbrella-like structures that will help guide us as well as the ones above.

So where is this? Top left may give it away to people. However, if not, we can Google `Clarke Quay Central Riverside Point` and we'll get our answer quickly.

![[searchlight_39.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_39.png)

The, possibly obvious, other way to discover the location: the famous Marina Bay Sands Resort.

![[searchlight_40.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_40.png)

OK, Cool. Now, let's look for Riverside Point in Clarke Quay and see if we can find the hotel.

When we find the location on Google Maps, we'll try to orient ourselves based on the river and the location from where the video was taken.

![[searchlight_41.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_41.png)

Above we can see, circled in green `Riverside Point`. In the pink we can see the umbrella structures and to the left circled in blue seems to be the building from where the video most likely was shot. From that position we would be looking down on the umbrellas and across the river to `Riverside Point`.

If we use street view and jump down between the umbrellas and the building to `Tan Tye Pl`, we'll see the pastel buildings from the video.

![[searchlight_42.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_42.png)

If we turn around we'll see something... 

![[searchlight_43.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_43.png)

Huh!? 

This is an image from March 2022, so we have a new development occurring in the location of the hotel. However, the internet never forgets so  we should still be able to find what was on the lot before `Canninghill Piers` started development. 

Google Dorking is cool, but, we can just ask Google questions too.

![[searchlight_44.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_44.png)

What buildings were on the `Liang Court` site? We are looking for the name of a hotel.

![[searchlight_45.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_45.png)

![[searchlight_46.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_46.png)

Now if we Google `Singapore Clarke Quay Novotel Hotel` we'll find a `Trivago` entry for the hotel.

![[searchlight_47.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_47.png)

A photo of the view from the hotel is taken at a similar angle to the video we watched. 

![[searchlight_48.png]](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/TryHackMe/Searchlight/images/searchlight_48.png)

We have enough by now to confirm that we have the correct hotel.

**Question:** `What is the name of the hotel that my friend stayed in a few years ago?`<br />
**Answer:** `sl{Novotel Singapore Clarke Quay}`<br />


