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

### Step Three — Add Public Key Authentication

The next step in securing your server is to set up public key authentication for your new user. Setting this up will increase the security of your server by requiring a private SSH key to log in.

#### Generate a Key Pair

If you do not already have an SSH key pair, which consists of a public and private key, you need to generate one. If you already have a key that you want to use, skip to the *Copy the Public Key* step.

To generate a new key pair, enter the following command at the terminal of your **local machine** (ie. your computer):

```
$ ssh-keygen
```

Assuming your local user is called “localuser", you will see output that looks like the following:

```
ssh-keygen output:
------------------------
Generating public/private rsa key pair.
Enter file in which to save the key (/users/localuser/.ssh/id_rsa):
```

Hit return to accept this file name and path (or enter a new name).

Next, you will be prompted for a passphrase to secure the key with. You may either enter a passphrase or leave the passphrase blank.

> **Note:** If you leave the passphrase blank, you will be able to use the private key for authentication without entering a passphrase. If you enter a passphrase, you will need both the private key and the passphrase to log in. Securing your keys with passphrases is more secure, but both methods have their uses and are more secure than basic password authentication.

This generates a private key, ```id_rsa```, and a public key, ```id_rsa.pub```, in the ```.ssh``` directory of the *localuser's* home directory. Remember that the private key should not be shared with anyone who should not have access to your servers!

#### Copy the Public Key

After generating an SSH key pair, you will want to copy your public key to your new server.

Create a new directory called ```.ssh``` and restrict its permissions with the following commands:

```
$ mkdir ~/.ssh
$ chmod 700 ~/.ssh
```
Now open a file in ```.ssh``` called ```authorized_keys``` with a text editor and paste the public key into it. Now restrict the permissions of the *authorized_keys* file with this command:

```
$ chmod 600 ~/.ssh/authorized_keys
```

Now your public key is installed, and you can use SSH keys to log in as your user.

### Step Four — Disable Password Authentication

Now that your new user can use SSH keys to log in, you can increase your server's security by disabling password-only authentication. Doing so will restrict SSH access to your server to public key authentication only. That is, the only way to log in to your server (aside from the console) is to possess the private key that pairs with the public key that was installed.

> **Note:** Only disable password authentication if you installed a public key to your user as recommended in the previous section, step four. Otherwise, you will lock yourself out of your server!

To disable password authentication on your server, follow these steps.

As **your new sudo user**, open the SSH daemon configuration:

```
$ sudo vi /etc/ssh/sshd_config
```

Find the line that specifies ```PasswordAuthentication```, uncomment it by deleting the preceding ```#```, then change its value to "no".

Here are two other settings that are important for key-only authentication and are set by default. If you haven't modified this file before, you **do not** need to change these settings:

```
PubkeyAuthentication yes
ChallengeResponseAuthentication no
```

Type this to reload the SSH daemon:

```
$ sudo systemctl reload sshd
```

Password authentication is now disabled. Your server is now only accessible with SSH key authentication.

### Step Five — Set Up a Basic Firewall

Ubuntu 16.04 servers can use the UFW firewall to make sure only connections to certain services are allowed. We can set up a basic firewall very easily using this application.

Different applications can register their profiles with UFW upon installation. These profiles allow UFW to manage these applications by name. OpenSSH, the service allowing us to connect to our server now, has a profile registered with UFW.

You can see this by typing:

```
$ sudo ufw app list
```

```
Output
-------------------
Available applications:
  OpenSSH
```

We need to make sure that the firewall allows SSH connections so that we can log back in next time. We can allow these connections by typing:

```
$ sudo ufw allow OpenSSH
```

Afterwards, we can enable the firewall by typing:

```
$ sudo ufw enable
```

Type "y" and press ENTER to proceed. You can see that SSH connections are still allowed by typing:

```
$ sudo ufw status
```

```
Output
-------------------
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
```
