![natas24_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas24_01.png)

More passwords, more problems?

![natas24_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas24_02.png)

Source code says!!!

![natas24_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas24_03.png)

It censored the password. How rude!

That doesn't mean we can't beat this login though. You might think that brute-forcing the input could help but, remember the passwords for these levels are all 32 characters long and `a-zA-Z0-9`, the chances we'll beat that any time soon is low.

Note that the comparison is done with `strcmp` (string compare). This is a pretty vulnerable function. It will return `0` if both strings it compares are equal. The issue that we can take advantage of is that `strcmp`, if given something it cannot compare against, will return `0` because the comparison isn't done with strict typing. This attack takes advantage of a type juggling vulnerability in `PHP`, through `strcmp`.

You can read more about type juggling [here](https://owasp.org/www-pdf-archive/PHPMagicTricks-TypeJuggling.pdf) but, put simply, in `php` there are two comparison operators: `loose (==)` and `strict (===)`. `strict` will make sure that the types and values being compared are the same. `loose` only checks the values and will involve dynamic type inference. `PHP` is a weak-typed language (variable types are inferred), compared to other languages like `C++` which is a strong-typed language (variable types must be supplied).

So, how can we use this to our advantage? 

Problems with `strcmp` can occur when the request parameter is manipulated to be an empty array and due to how `strcmp` parses it's arguments, it will result in bypassing authentication (`strcmp` will return `0`). 

![natas24_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas24_04.png)

The above user on `php.net` shows how using `strcmp` on something other than a string (an empty array) can be used to bypass a login.

That means if we supply `passwd` as `passwd[]`, `php` will hiccup and let us in.

![natas24_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas24_05.png)

As you can see, we simply set the parameter to `passwd[]` and the password can be anything we want it to be. The outcome, `strcmp` short-circuits and let's us bypass the login.
