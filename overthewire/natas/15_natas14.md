![natas14_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas14_01.png)

New level, new form!

![natas14_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas14_02.png)

This time we need a username and password to beat the level. Let's have a look at the source code.

![natas14_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas14_03.png)

We can see that the username and password are sent to a `SQL` (Structured Query Language) query and we control part of the command through the form fields.

- `SELECT * from users where username="<user_input>" and password="<user_input>"`

This may look a little odd, however, it's just one way that you can use `SQL` syntax to access all username's and password's from the user table in a `SQL` database.

We can manipulate this query by injecting arbitrary text into the command. This works because what we input into the form fields will be concatenated into the command string. Concatenating commands with user input in this way is a vulnerability that is open to an attack called `SQLi` (SQL injection). This attack can be used to login to a site without valid user credentials.

The simplest `SQLi` payload you will see will be one of the following:

- `" OR 1=1; --`
- `" OR 1=1 --`
- `' OR 1==1 #`

The specific syntax will depend on the database being used (`MySQL`, `PostgreSQL`, `MongoDB`, `SQLite`, `MariaDB`, `Oracle`, etc).

What does it do? 

Simply, it always returns `true` because `1` always equals `1`. The command we're injecting code into looks for a valid username and password from the users table in the `natas14` database. If we can trick it into thinking we supplied valid credentials, we're in.

The key here is we don't have to complete the command as it is expected by the database. We don't even have to supply a username or password at all. We can do something similar to what we did with `passthru` the first time we saw it; Inject the payload and then comment out the rest of the line. 

When we inject the payload it'll be concatenated into the command like this:

- `SELECT * from users where username="" OR 1=1 # and password=""`

Which will be parsed like this:

- `SELECT * from users where username="" OR 1=1 #`

Everything after the `#` is considered a comment and is dropped from the command.

So, if it finds the username we enter (it won't) OR `1=1` (which is always true), it'll return `true`. 

In another syntax (`C++`), we can express it like this:

```c++
// Always True
if ((username == "") || ("1" == "1")) {
        // Do login stuff
}
```

Let's try it.

I'll show you a few attempts because it's important to know that if something doesn't work on the first try, that doesn't mean you're not on the right path. Sometimes changing up the syntax a little will result in success. The Offsec (Offensive Security) [motto](https://www.offsec.com/offsec/what-it-means-to-try-harder/) "Try harder" applies here.

Let's see if our syntax and assumption of only needing the `username` is correct.

![natas14_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas14_04.png)

Nope! Access denied! Note that the error mentions that it received an unexpected result. 

![natas14_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas14_05.png)

How about we try both fields?

![natas14_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas14_06.png)

Nope, same error about the unexpected result.

Perhaps it's the syntax? Let's try with a different syntax and keep our assumption about the `username`.

![natas14_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas14_07.png)

Success!

![natas14_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas14_08.png)

Remember, try harder. If at first you don't succeed, try, try and try again. Think outside the box... and so on. 

All of these mottos (Offsec and otherwise) are worth remembering. Even simple things like one small change in syntax can challenge you - don't give up. If you need help, don't feel ashamed to seek it out. Writeups are here for that reason and taking hints is a better way to learn than banging your head against a wall. One grows brain cells, the other destroys them :P.

Hacking/Offsec is not easy but, it's very much worth your time, effort and persistence through each challenge to learn and get better. Just make sure to try to exhaust all your options before getting a hint.

---

# The Python Way

PSA over, now let's look at how we can achieve the attack by using `python`.

We only need the `username` in order for this to work as everything after `1=1` is commented out.

![natas14_09.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/natas/images/natas14_09.png)
