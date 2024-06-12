# My Droplet is sending an outgoing flood or DDoS :: DigitalOcean Documentation
    
    
If you’ve received a message from DigitalOcean support because your Droplet is sending an outgoing flood or DDoS, here are the next steps you need to take.

Why did I get this message?
---------------------------

You received this message because your Droplet is sending out an unusually large amount of traffic. In most cases, this indicates that your Droplet’s security has been compromised and that someone is using it to send spam email or malicious traffic.

To protect others from being harmed, your Droplet’s normal Internet access has been disabled, but you can still access it through [the Recovery Console](https://docs.digitalocean.com/products/droplets/how-to/recovery/recovery-console/).

What should I do next?
----------------------

We recommend that you create a plan to recover your content, get it back online, and resolve current and future security issues.

Each server setup is unique, but in many cases, these are the general steps you should follow:

### Recover Content

First, you need to access your server through [the Recovery Console](https://docs.digitalocean.com/products/droplets/how-to/recovery/recovery-console/). You need a password for root, so if you don’t have one, contact support for further advice.

After you get access to your server, you can [recover content from it using the recovery ISO](https://docs.digitalocean.com/products/droplets/how-to/recovery/recovery-iso/).

### Create a New Droplet

In most cases, it’s best to move to a fresh, uncompromised Droplet. Start by [creating a new Droplet](https://docs.digitalocean.com/products/droplets/how-to/create/), then secure it and redeploy your content.

To prevent your new Droplet from being compromised in the same way as your old one, we recommend following [good security practices](https://www.digitalocean.com/community/tutorials/an-introduction-to-securing-your-linux-vps), including:

*   Disabling the root user
*   [Using an SSH key](https://docs.digitalocean.com/products/droplets/how-to/add-ssh-keys/)
*   Keep your software up to date
*   Use strong passwords
*   Keep your [firewall](https://docs.digitalocean.com/products/networking/firewalls/) settings as strict as possible

After your new Droplet is secure, you need to redeploy your content. Your individual setup is unique in this case. As you install your software and upload your content, it’s a good idea to do a quick sanity check for security at each step. We recommend a few additional best practices:

*   Run the latest versions of software
*   Run applications as unprivileged users
*   Use strong passwords
*   Use certificates and keys where possible
*   Never use 777 permissions
*   Remove installation files and unused forms

### Conduct a Post-Mortem

Now that your content is back online, you have some breathing space to figure out what went wrong the first time. These steps can help you find evidence of viruses and Trojans on your old server.

If you find anything suspicious in this step that could lead to a similar compromise on your new Droplet, take steps to prevent the issue from recurring.

#### Find Processes

Once you are [logged in to your compromised Droplet from the console](https://docs.digitalocean.com/products/droplets/how-to/recovery/recovery-console/), use one of these commands to try to find an unfamiliar process running.

This command, if installed, shows programs holding open a network socket:

```
lsof -i

```


This command shows all running processes:

```
ps -ef

```


Adding a pipe to an output paging program may help for long output, as in:

```
lsof -i | less
ps -ef | less

```


At this point, you may have a process or two that you want to investigate. Make a note of the process ID for each one, which is a string of numbers.

#### Find Files

Next we want to find the malicious files on your system.

You can use this command to locate the executable file that is the origin of a particular process. Replace `process_id` with the process ID (PID) you found earlier:

```
ls -al /proc/process_id/exe

```


You can repeat this command for any suspicious processes you noted earlier.

You can also search for suspicious files yourself. Common places Trojans hide are:

*   `/boot`
*   `/tmp`
*   `/run`
*   `/root`

You can use this command to list all content in a particular folder, including dot files. This example is for the `/boot` directory:

```
ls -al /boot

```


#### Further Sleuthing

If you find something foreign, check the ownership of the files for hints on what user was used to install the malicious code. Review your log files to try to find out how the code was installed so that you can work on preventing it from happening again.

If you need any advice, [open a support ticket](https://cloudsupport.digitalocean.com/) with the data you need help with. The best way is to screenshot the console showing the data you are uncertain of, upload to a file sharing service (imgur.com, dropbox.com, etc.) and include the URL in the ticket.

Some programs that may also help are Rootkit Hunter, chkrootkit, Linux Malware Detect (LMD), and ClamAV’s `clamscan`.

If you can’t find anything, you can open [a support ticket](https://cloudsupport.digitalocean.com/) for advice.

#### Wrapping Up

Now you can kill any malicious processes and remove the files.

Double-check your new Droplet to make sure nothing suspicious was copied over. If you identified the user or program that was compromised the first time, take extra steps to secure that user or program on the new server.

### Make Backups

Security is important, and so is being prepared. Should your Droplet ever become compromised again in the future, [backups](https://docs.digitalocean.com/products/backups/) can make your recovery process much easier. We recommend acquainting yourself with [backup strategies](https://www.digitalocean.com/community/tutorials/how-to-choose-an-effective-backup-strategy-for-your-vps) and choosing the one that’s right for you.

We temporarily trigger a blackhole when a DDoS attack against a resource reaches a mitigation limit. We recommend contacting support and planning strategies to keep your resources online in the future.
