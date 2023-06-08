![bandit21_01.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit21_01.png)

This level is using `cronjobs`, basically the Linux version scheduled tasks on Windows. We can read the `crontab` file to find if any task has been scheduled and how often it is run.

We can read the `crontab` file by using `cat` on `/etc/crontab`

![bandit21_02.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit21_02.png)

There isn't anything here that stands out. You would usually see custom tasks below the `/usr/sbin/anacron` jobs.

However, the introduction tells us to check in the `cron.d` (cron daemon) directory. 

![bandit21_03.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit21_03.png)

We can see a listing of all the `cronjobs` here. Currently we are the user `bandit21` and we're looking to find the password to `bandit22`. There just so happens to be a job for this user in the file called `cronjob_bandit22` and we can read it.

Traverse to the `/etc/cron.d` directory and read the file.

![bandit21_04.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit21_04.png)

The syntax for the file is similar to the `crontab` file. The asterisks on the left are related to when the job is run. 

```
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
```

When an asterisk is supplied it means "every tick of time" per it's type. Meaning the job will execute every minute, on every hour, every day of the month, every month and every day of the week. To simplify, all asterisks means the job runs every minute. The `user-name` is the name of the user that the job runs as and the command is the job that will be executed.

So, here, `/usr/bin/cronjob_bandit22.sh` runs every minute as the user `bandit22`. If you have trouble understanding the readout you can use [Crontab.guru](https://crontab.guru/) to help you out.

Now, let's check the permissions on the file that runs.

![bandit21_05.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit21_05.png)

We can see that as `bandit21` we can read and execute the file. So, let's read it.

![bandit21_06.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit21_06.png)

The script changes the permissions on a file in the temp directory and places the password for `bandit22` into that file. If you know your permissions well you'll know that `644` means everyone has read access to this file. 

![bandit21_07.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit21_07.png)

Cool! Let's read the file. 

![bandit21_08.png](https://raw.githubusercontent.com/ToasterMouse/WriteupsAndCTFs/main/overthewire/bandit/images/bandit21_08.png)

And it's on to the next level: `level22 -> level23`.
