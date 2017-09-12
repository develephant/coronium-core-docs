__Coronium Core__ server is eligible for the EC2 free tier using Amazon Web Services.

!!! warning "Important Note"
    This guide assumes that you have an active AWS account, and are familiar with managing EC2 instances.

!!! note "Screencast"
    View the installation instructions in a screencast format by [clicking here](https://www.youtube.com/watch?v=_PhYz5T7OK4).

### Create An Instance

Once you log into the __[AWS Console](https://aws.amazon.com/console/)__, navigate to the __EC2__ service. Click the __Launch Instance__ button.

On the next screen, find the __Ubuntu Server 16.04 LTS (HVM)__ AMI, and click the __Select__ button on the right.

Select your preferred instance type. A __t2.micro__ is a good starting point. You can always increase the size later.

Using the __Add Rule__ button on the __Configure Security Group__ screen, and add the following rules:

|Type|Protocol|Port Range|Source|Description|
|----|--------|----------|------|-----------|
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

`wget https://s3.amazonaws.com/coronium-core/ec2/up.sh && sudo bash up.sh`

### Database Selection

At the start of the installation you will be prompted to choose the databases to install with your __Coronium Core__ server. You can select both __MySQL__ and __Mongo__ databases, or if you are only planning on using one kind, then select accordingly.

!!! tip ""
    You can conserve server memory by using only one database type.

!!! failure "Important"
    __You must select at least one database type or the installation will fail.__ Choose wisely, you cannot install an additional database after the the selection process has completed.

### Database Passwords

Once the installation is complete, you will be prompted for your choice of database passwords. Make sure they are secure, and don't lose them. You will use these passwords to connect to your databases both internally and externally.

Once the installation is complete, take a look at the __[Post Install](/server-install/postinstall/)__ section.