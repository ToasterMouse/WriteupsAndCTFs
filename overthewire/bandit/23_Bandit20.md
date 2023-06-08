![bandit20_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit20_01.png)

We are told about another `setuid` binary, but this time it will send us the password once it receives our current password. This works a little different to how we might first assume it works. Let's connect to the machine and see what's up.

We will find the `setuid` binary in the home directory and when we run it we're told that it connects to a port on `localhost` and awaits the password to be sent to it.

![bandit20_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit20_02.png)

The key thing here is that it mentions that it uses TCP (Transmission Control Protocol). Therefore, we should be able to beat the level by setting up a `netcat` listener. This is simply a connection that we can use to communicate with the binary over TCP/IP.

It expects input from the "other side" so once it connects to our listener, we can send `suconnect` the password. We will need two sessions for this and thankfully the `bandit` machine comes with `tmux`.

I won't cover the ins and outs of `tmux` but [IppSec](https://www.youtube.com/watch?v=Lqehvpe_djs) has a great introduction to `tmux` worth watching. Also, this [cheatsheet](https://tmuxcheatsheet.com/) can help you get started.

You can create a new `tmux` session with `tmux new-sess`, you can also give it a name by supplying `-t <name>` if you want. Also, you can open a new horizontal window pane by pressing `ctrl+b shift+"`. We will need two window panes to beat this level.

Your terminal should now look something like this:

![bandit20_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit20_03.png)

We can switch between each window pane by using `ctrl+b <arrow_up>/<arrow_down>`. If you're confused, that's OK. But, it's definitely worth your effort to check out and learn to use `tmux`.

Now we're set up the first thing we need to do is grab the `bandit20` password. We already have it in our own `bandit20` file but we can also find it in `/etc/bandit_pass/bandit20`. Using the file on the system will make this easier.

The next thing we want to do is to set up a listener. We can do that with `nc -lvp 7777`. This means we are listening (`-l`), verbosely (`-v` - in greater detail, basically) and on port 7777 (`-p 7777`). To this we will redirect the file `/etc/bandit_pass/bandit20`. What this will do is when a connection is made to our listener, the content of the password file will be given to the connecting side without us having to copy and paste anything.

![bandit20_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit20_04.png)

Now all we need to do is make the connection with `./suconnect 7777`.

![bandit20_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit20_05.png)

Just like that, we now have the next password.

As you can see, once we connect with `suconnect` the `bandit20` password is sent and `suconnect` acknowledges this with `Read: ` and `Password matches`. It then sends the next password to our listener, which appears just below `Connection received on localhost 39258`.

To exit the `tmux` session use: `ctrl+b d` and close the session with `tmux kill-session -t <name_of_session>`. Say you named the session `h4ck3d` it would be `tmux kill-session -t h4ck3d`. Now you can exist the `SSH` session normally, with `exit`.

Onto the next level: `level21 -> level22`.


