# <i class="fab fa-digital-ocean"></i> DigitalOcean

__Coronium Core__ runs best on a __[DigitalOcean](https://m.do.co/c/cddeeddbbdb8) Ubuntu 16.04__ droplet.

!!! note "Screencast Available"
    Watch the installation instructions in a screencast format by __[Clicking here](/screencasts/#digitalocean-installation)__.

### Create A Droplet

!!! tldr "Free Droplets"
    If you're new to [DigitalOcean](https://m.do.co/c/cddeeddbbdb8) please consider signing up with __[this link](https://m.do.co/c/cddeeddbbdb8)__. Not only will you receive a $10 credit, but it also helps support the continued development, and testing of __Coronium Core__.

Once you log into your [DigitalOcean](https://m.do.co/c/cddeeddbbdb8) account, click the __Create__ button and select __Droplets__ from the menu.

![step1](/imgs/step01.png)

On the next screen, first select a __Ubuntu 16.04__ droplet distribution.

![step2](/imgs/step02.png) 

Select the droplet size. __A 1 GB/1 CPU droplet is recommended as a minimum__.

![step3](/imgs/step03.png)

Next, select a region for the droplet. Consider choosing a location closest to your most active user base.

![step4](/imgs/step04.png)

Select your SSH profile to attach to the droplet.

![step5](/imgs/step05.png)

!!! tldr "Password Option"
    There is an option where you can use a password instead of an SSH key, which may be easier if you're only testing __Coronium Core__, though I wouldn't recommend it. You can learn more about generating SSH keys for [DigitalOcean](https://m.do.co/c/cddeeddbbdb8) here: [Creating SSH Keys](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets).

Now give your droplet a hostname.

![step6](/imgs/step06.png)

_Be sure to replace __coronium.develephant.com__ with your own hostname._

And finally, click the __Create__ button to spin up the droplet.

![step7](/imgs/step07.png)

### Install Coronium Core

Once your droplet is done spinning up, note the ip address.

![step8](/imgs/step08.png)

Using a terminal/shell of your choice, SSH into the droplet.

!!! tip
    You can use the built in terminal on OSX. For Windows, check out __[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)__.

```
ssh root@<your-instance-ip>
```

_Replace __<your-instance-ip\>__ with the address that was assigned to your droplet._

Once you are connected to the droplet, copy and paste the following line into the terminal:

```
wget https://s3.amazonaws.com/coronium-core/do.sh && sudo bash do.sh
```

### Database Passwords

Once the installation is complete, you will be prompted for your choice of database passwords. Make sure they are secure, and don't lose them. You will use these passwords to connect to your databases both internally and externally. 

!!! tldr "Changing Passwords"
    If you need to change the passwords later see __[Database Passswords](/server/guide/usage/#database-passwords)__ in the server guide.

### Server Key

The server key is used by the Coronium Core client-side _[initialization](/client/guide/#initialization)_ method. This key is presented at the end of the installation. Copy the key to use now so you won't have to re-log into the server. To retrieve the key later, use the coronium tool on the server (see __[Server Key](/server/guide/key/)__).

### Next Steps

Once the installation is complete, take a look at the __[Post Install](/server/installation/postinstall/)__ section.