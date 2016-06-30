# Initial Server Setup with Ubuntu 16.04

### Step One — Create a New User (only required if you didn’t setup a regular user during install)

Once you are logged in as `root`, we're prepared to add the new user account that we will use to log in from now on.

This example creates a new user called "sammy", but you should replace it with a username that you like:

```
$ adduser sammy
```
You will be asked a few questions, starting with the account password.

Enter a strong password and, optionally, fill in any of the additional information if you would like. This is not required and you can just hit ```ENTER``` in any field you wish to skip.

### Step Two — Root Privileges

Now, we have a new user account with regular account privileges. However, we may sometimes need to do administrative tasks.

To avoid having to log out of our normal user and log back in as the root account, we can set up what is known as "superuser" or root privileges for our normal account. This will allow our normal user to run commands with administrative privileges by putting the word ```sudo``` before each command.

To add these privileges to our new user, we need to add the new user to the "sudo" group. By default, on Ubuntu 16.04, users who belong to the "sudo" group are allowed to use the ```sudo``` command.

As ```root```, run this command to add your new user to the sudo group (substitute the highlighted word with your new user):

```
$ usermod -aG sudo sammy
```
