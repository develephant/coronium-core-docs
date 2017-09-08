__Coronium Core__ runs best on a __[DigitalOcean](https://m.do.co/c/cddeeddbbdb8) Ubuntu 16.04__ droplet.

!!! tip
    If you're new to [DigitalOcean](https://m.do.co/c/cddeeddbbdb8) please consider signing up with __[this link](https://m.do.co/c/cddeeddbbdb8)__. Not only will you receive a $10 credit (2 free months), but it also helps support the continued development, and testing of __Coronium SkyTable__.

### Create A Droplet

Once you log into your [DigitalOcean](https://m.do.co/c/cddeeddbbdb8) account, click the __Create__ button and select __Droplets__ from the menu.

![step1](../imgs/step01.png)

On the next screen, first select a __Ubuntu 16.04__ droplet distribution.

![step2](../imgs/step02.png) 

Select the droplet size. A __1 GB/1 CPU__ droplet is recommended as a minimum. If you're only testing, you can use a 512 MB droplet and increase the size later.

![step3](../imgs/step03.png)

Next, select a region for the droplet. Consider choosing a location closest to your most active user base.

![step4](../imgs/step04.png)

Select your SSH profile to attach to the droplet.

![step5](../imgs/step05.png)

!!! caution
    There is an option where you can use a password instead of an SSH key, which may be easier if you're only testing __Coronium Core__, though I wouldn't recommend it. You can learn more about generating SSH keys for [DigitalOcean](https://m.do.co/c/cddeeddbbdb8) here: [Creating SSH Keys](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets).

Now give your droplet a hostname.

![step6](../imgs/step06.png)

_Be sure to replace __coronium.develephant.com__ with your own hostname._

And finally, click the __Create__ button to spin up the droplet.

![step7](../imgs/step07.png)

### Install Coronium Core

Once your droplet is done spinning up, note the ip address.

![step8](../imgs/step08.png)

Using a terminal/shell of your choice, SSH into the droplet.

!!! tip
    You can use the built in shells on both OSX and Linux. For Windows, check out __[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)__.

```
ssh root@<your-instance-ip>
```

_Replace __<your-instance-ip\>__ with the address that was assigned to your droplet._

Once you are connected to the droplet, copy and paste the following line into the terminal:

`curl -LO https://s3.amazonaws.com/coronium-core/up.sh && sudo bash ./up.sh`

Once the installation is complete, continue on to the __[Post Install](/server-install/postinstall/)__ section.