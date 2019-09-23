# <i class="fab fa-amazon"></i> Amazon Lightsail

__Amazon Lightsail__ is a simplified interface for using EC2.

!!! info ""
    __Coronium Core__ server is eligible for the free tier using Amazon Web Services. 

### Create An Instance

  - Log into your AWS account and __[open Amazon Lightsail](https://lightsail.aws.amazon.com)__.

  - Click the "Create Instance" button.

  - Choose "Linux/Unix" under the "Select a platform" section.

  - Under "Select a blueprint" click "OS Only" and choose __Ubuntu 16.04 LTS__.

  - Under "Choose your instance plan" select at minimum a 2GB ($10) instance. _Note: A 1GB instance will work for testing the platform._ 

  - In the "Identify your instance" section enter a name for your __Coronium Core__ server.

  - Click the __Create Instance__ button to start the instance creation process.

### Configure An Instance

  - Once the instance is in a "Running" state, select the instance by clicking its name. 
  
  - On the overview page, select the "Networking" section.

  - Under the "Firewall" section, add the following by using the "+ Add another" link:

    - [+Add another] -> From the "Application" dropdown, select __HTTPS__.
    - [+Add another] -> From the "Application" dropdown, select __MySQL/Aurora__.
    - [+Add another] -> From the "Application" dropdown, select __Custom__, and then enter __27017__ in the "Port range" field.
    - [+Add another] -> From the "Application" dropdown, select __Custom__, and then enter __10001__ in the "Port range" field.

### Install Coronium Core

  - Return to the overview page for your instance and note the "Public IP".

  - Click the __Connect using SSH__ button to connect to the new instance.

  - Once you are connected to the instance, copy and paste the following line into the terminal:

```
wget https://s3.amazonaws.com/coronium-core/ec2.sh && sudo bash ec2.sh
```

### Database Passwords

Once the installation is complete, you will be prompted for your choice of database passwords. Make sure they are secure, and don't lose them. You will use these passwords to connect to your databases both internally and externally.

!!! tldr "Changing Passwords"
    If you need to change the passwords later see __[Database Passswords](/server/guide/usage/#database-passwords)__ in the server guide.

### Server Key

The server key is used by the Coronium Core client-side _[initialization](/client/guide/#initialization)_ method. This key is presented at the end of the installation. Copy the key to use now so you won't have to re-log into the server. To retrieve the key later, use the coronium tool on the server (see __[Server Key](/server/guide/key/)__).

### Connecting

Most often if you need to work with something on the instance, you will use the __coronium__ user to log in (which was created on install). The __ubuntu__ user is used for updating, and system level operations.

To connect with the __coronium__ user, you will need to use a shell program on your local machine.

!!! tip
    You can use the built in terminal on OSX. For Windows, check out __[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)__.

```
ssh -i <path/to/.pem> coronium@<your-instance-ip>
```

_If you don't know your SSH Key (.pem), navigate to your [Lightsail Account](https://lightsail.aws.amazon.com/ls/webapp/account/keys) page.  Download your key under the "SSH Keys" tab._

### Next Steps

Once the installation is complete, take a look at the __[Post Install](/server/installation/postinstall/)__ section.