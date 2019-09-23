# <i class="fab fa-amazon"></i> Amazon EC2

__Coronium Core__ server is eligible for the EC2 free tier using Amazon Web Services.

!!! warning "AWS Experience Required"
    This guide assumes that you have an active AWS account, and are familiar with managing EC2 instances.

### Create An Instance

Once you log into the __[AWS Console](https://aws.amazon.com/console/)__, navigate to the __EC2__ service. Click the __Launch Instance__ button.

On the next screen, find the __Ubuntu Server 16.04 LTS (HVM)__ AMI, and click the __Select__ button on the right.

Select your preferred instance type. A __t2.micro__ is a good starting point. You can always increase the size later.

Using the __Add Rule__ button on the __Configure Security Group__ screen, and add the following rules:

|Type|Protocol|Port Range|Source|Description|
|----|--------|----------|------|-----------|
|Custom TCP|TCP|80|Anywhere|_HTTP support_|
|Custom TCP|TCP|443|Anywhere|_HTTPS support_|
|Custom TCP|TCP|10001|Anywhere|_Coronium Core access_|
|Custom TCP|TCP|3306|Anywhere or IP|_MySQL server admin_|
|Custom TCP|TCP|27017|Anywhere or IP|_Mongo server admin_|

Adjust any additional settings, and then __Launch__ the AMI.

### Install Coronium Core

Once your instance is in a __running__ state, note the __IPv4 Public IP__ address.

Using a terminal/shell of your choice, SSH into the instance.

!!! tip
    You can use the built in terminal on OSX. For Windows, check out __[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)__.

```
ssh -i <path/to/.pem> ubuntu@<your-instance-ip>
```

_Replace __<your-instance-ip\>__ with the address that was assigned to your instance._

Once you are connected to the droplet, copy and paste the following line into the terminal:

```
wget https://s3.amazonaws.com/coronium-core/ec2.sh && sudo bash ec2.sh
```

### Database Passwords

Once the installation is complete, you will be prompted for your choice of database passwords. Make sure they are secure, and don't lose them. You will use these passwords to connect to your databases both internally and externally.

!!! tldr "Changing Passwords"
    If you need to change the passwords later see __[Database Passswords](/server/guide/usage/#database-passwords)__ in the server guide.

### Server Key

The server key is used by the Coronium Core client-side _[initialization](/client/guide/#initialization)_ method. This key is presented at the end of the installation. Copy the key to use now so you won't have to re-log into the server. To retrieve the key later, use the coronium tool on the server (see __[Server Key](/server/guide/key/)__).

### Next Steps

Once the installation is complete, take a look at the __[Post Install](/server/installation/postinstall/)__ section.