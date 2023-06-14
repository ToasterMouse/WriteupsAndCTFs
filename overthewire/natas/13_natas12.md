![natas12_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_01.png)

This time when we take a look at the website we can see that we have a way to upload a file...

![natas12_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_02.png)

...and the source code is getting longer and longer.

![natas12_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_03.png)

We have a few functions we need to look at: `genRandomString()`, `makeRandomPath()` and `makeRandomPathFromFilename`. When we upload a file this code will generate a new filename for the file in the `upload` directory. Once it has been uploaded the file path will be displayed on the page for us.

Also, the file cannot be over `1kb` otherwise it won't get uploaded.

If we manipulate this code a little, we'll see what we can expect to occur when we upload a file.

![natas12_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_04.png)

All I did was remove the `POST` request stuff, give a custom filename and extension to `makeRandomPathFromFilename` and echo the result of that function.

![natas12_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_05.png)

It will be what we expected, the filename is changed to a random value and it maintains it's extension. This may seem trivial but understanding what we're looking at is highly important. We can make really simple mistakes by making too many assumptions about the code we are analysing. A sanity check like the one we performed above will keep us on the right track.

We know that the `php` code works in a specific way, however, look a little further down in the source code at where the form is handled.

![natas12_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_06.png)

We can see that the value of the `filename` field is set to a random string and the extension is `.jpg`. If we upload something, we can test if it does generate a `jpg` or simply change our extension. Let's upload a test file that includes some `php`.

Our file will contain a single line.
```php
<?php phpinfo(); ?>
```

The `phpinfo()` function will return information on the site's `php` configuration. It's a decent and very obvious way to test if we can execute `php` on a site. You'll see what I mean by obvious in just a second.

![natas12_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_07.png)

We'll upload the file.

![natas12_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_08.png)

We can see that it does get renamed to a `.jpg`.

![natas12_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_09.png)

Also, it cannot be read. Therefore, we can conclude that it doesn't convert the file to `jpg` but, simply changes the name and extension.

It looks like the filename and extension are getting set here, as we figured.

![natas12_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_06.png)

Let's intercept the upload to the site to confirm how the file is actually getting uploaded. We will use `burpsuite` for this. If you are not familiar with `burpsuite`, I'll cover some basics as we go along. 

To set it up you can to use the `foxyproxy` extension for your browser. You can download it from your browser's extension/web store.

Once you have it, create an entry for `burp` by selecting the extension and clicking the `options` tab. Then create a new entry, it should look like this:

![natas12_10.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_10.png)

Save it and exit the options tab. Turn on the proxy by selecting the profile you just made from the extension window and then start `burpsuite`.

![natas12_11.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_11.png)

Then go to `http://burpsuite` in your browser and click the `CA Certificate` link in the top right to download the certificate.

![natas12_12.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_12.png)

If `burpsuite` opens up (which it probably will as it should have intercepted the request you just made), turn `intercept` off in the `Proxy` tab.

![natas12_15.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_15.png)

Now, head to your browsers settings (I'm doing this in Firefox, it should be similar in other browsers), you can search for certificates in the search bar and select `View Certificates`.

![natas12_13.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_13.png)

Lastly, `import` the certificate you just downloaded.

![natas12_14.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_14.png)

You will have to accept the next pop-up and make sure to select that you trust the certificate.

Now you will be ready to intercept requests. 

Make sure to turn `intercept` back on and reload the page. When it reloads `burpsuite` will pop up showing the intercepted request.

![natas12_16.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_16.png)

For now just `Forward` the request. Then go to the `Target` tab and right-click the site we're dealing with and select `Add To Scope`. 

![natas12_17.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_17.png)

OK! Phew! Now we can upload our `php` file again. This time you'll see we've intercepted the upload form.

![natas12_18.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_18.png)

Notice that the new filename is present and we can also see the content of the file.

The great thing about `burpsuite` is not only can we intercept requests like this but, we can manipulate them. Let's change the filename to have the `php` extension.

![natas12_19.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_19.png)

Now `Forward` the request or just turn `intercept` off. Then check the page.

![natas12_20.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_20.png)

Ah ha! The file has the correct extension! Also, note the filename is different than what we can see in `burpsuite`. It looks like the site is filtering for `jpgs` using the form. The `php` code, therefore, will generate a new filename but use the extension that the form gives it.

That's good to know. Let's see if we can read the file we just uploaded.

![natas12_21.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_21.png)

It's the `phpinfo()` readout. Nice! See what I mean by obvious? We know we can execute `php` on the site so, what do we want to do?

We want to read the password, right? These are kept in `/etc/natas_webpass/<natas_user>`. 

Let's make another `php` file. This time the payload will simply use the `system` function to open the file (we've seen `system` before - it's like `passthru`).

```php
<?php system("cat /etc/natas_webpass/natas13"); ?>
```

Make sure that `intercept` is turned on.

![natas12_22.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_22.png)

Upload the file and change it's extension.

![natas12_23.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_23.png)

We can see the upload was successful!

![natas12_24.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_24.png)

Now access the file for the win.

![natas12_25.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_25.png)

---

# The Python Way

Of course, we can achieve the same thing with python. We'll make sure we still have our file to upload and send it like this:

![natas12_26.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_26.png)

We can upload files through `requests` by using the `files` parameter, here we set the content of `passwd.php` to the `uploadedfile` parameter. This is similar to how the data gets added in the intercepted request.

![natas12_23.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas12_23.png)

Next, we set the `filename` for the request and because we're not submitting the data through the form but directly to the site using the specific parameters it uses, we don't have to worry about dealing with the form-appended `jpg` extension (nice!).

Lastly, our script grabs the filename and we set a new `URL` to access the file which we do with another request, this time with `get`.
