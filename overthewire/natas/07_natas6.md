![natas6_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas6_01.png)

Here we have a slightly different page than what we've seen up until now.

![natas6_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas6_02.png)

We can see that there is now an input form and a link to view the source code.

![natas6_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas6_03.png)

The reason why that link is there is because the code that controls how the form works is in `PHP`. Unlike the other languages we've seen so far, `PHP` doesn't show up in the source code. Instead it gets parsed and the result is rendered on the page. The source code in this case has been specially handled so we can see it and analyze it.

```php
<?      
include "includes/secret.inc";

if(array_key_exists("submit", $_POST)) {           
   if($secret == $_POST['secret']) {            
       print "Access granted. The password for natas7 is <censored>";       
   } else {           
       print "Wrong secret";       
   }       
} 
?>
```

Thankfully, the code is fairly easy to understand.

```php
include "includes/secret.inc";
```

Firstly, it includes a file called `secret.inc`. Includes are like python modules or other imports in other languages. Typically, they are libraries of code that developers can use to make their applications work as they intended without needing to go through the effort of creating/redefining simple functions themselves. When files are added this way all their code is accessible from within the script in which they were included.

```php
if(array_key_exists("submit", $_POST)) {           
   if($secret == $_POST['secret']) {            
       print "Access granted. The password for natas7 is <censored>";       
   } else {           
       print "Wrong secret";       
   }       
}  
```

When a `POST` request is made using the form, the value in the input field is checked (referred to here as `secret`) and if it matches the predefined value held in the variable `$secret` we are given the password to the next level.

Where is this `$secret` variable defined then? Remember that included files contain code, so, we can potentially read what the code is if we have access to the included file.

Let's head to `includes/secret.inc`. We won't see anything when we get there, but if we check the source code of this file we'll find out how the secret has been defined.

![natas6_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas6_04.png)

Now, we want to submit this to the input field. Just making sure we're all square, we can check the form and see that the input field name is indeed called `secret`. 

![natas6_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas6_05.png)

OK, everything should work as we expect.

Enter the secret.

![natas6_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas6_06.png)

Submit the query.

![natas6_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas6_07.png)

???

Profit.
