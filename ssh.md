# SSH

## Using SSH to connect to DigitalOcean droplets [Link](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets)

Note: this guide mainly only applies to Mac OS X/ Linux users

### Quick Start

1. Create the RSA Key Pair
2. Store the Keys and Passphrase (optional)
3. Copy the SSH Keys
4. Create a new Server (droplet)
5. Connect to your Server

---

### Details

- Create the RSA Key Pair
  - Generate the Key Pair using `ssh-keygen -t rsa`.

- Store the Keys and Passphrase
  - You will be prompted `Enter file in which to save the key (/demo/.ssh/id_rsa):` where you can either press `enter` to leave it as the default or specify your own directory and filename.  
  - You will be prompted to enter a `passphrase`. It is optional and you can leave it blank.
  - The entire process will look like the following and by default, the `public key` can be found at `~/.ssh/id_rsa.pub` and the `private key` can be found at `~/.ssh/id_rsa`.

```ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/demo/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /demo/.ssh/id_rsa.
Your public key has been saved in /demo/.ssh/id_rsa.pub.
The key fingerprint is:
4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 demo@a
The key's randomart image is:
+--[ RSA 2048]----+
|          .oo.   |
|         .  o.E  |
|        + .  o   |
|     . = = .     |
|      = S = .    |
|     o + = +     |
|      . o + o .  |
|           . o   |
|                 |
+-----------------+
```
- Copy the SSH Keys
  - Add your SSH public key to the Droplet through DigitalOcean's control panel
  - You can use `cat ~/.ssh/id_rsa.pub` to get the contents of your `public key` assuming you have left it as the default filename.  

- Create a new Server (droplet)
  - The previous step allows you to add the SSH key to your newly created droplet. However, to add a SSH key to a pre-existing droplet, you can use the following command:  
  `cat ~/.ssh/id_rsa.pub | ssh root@[your.ip.address.here] "cat >> ~/.ssh/authorized_keys"`.  

- Connect to your Server
  - After you have created your server with the SSH keys pre-installed, you can connect to it the same way as before:  
  `ssh root@[your.ip.address.here]`.

- Lockdown Root SSH Access to Keys Only
  - After confirming that you are able to login to the server as `root` **without** being prompted for a password, you should disable `password logins` for `root`. This will make your server more secure.
  - Edit the server's SSHd configuration at `/etc/ssh/sshd_config` and update the following line to now read:  
  `PermitRootLogin without-password`
  - Restart or rehup the `sshd` process to have it re-read the new configuration file using `ps auxw | grep ssh` to get the `pid` of the process. Then, run `kill -HUP <pid>`.

