## Reboot

After the __Coronium Core__ installation runs, it is wise to reboot the server instance, so that you can free up any memory that may have been used during the installation process.

On the command line, enter:

```
sudo reboot -h
```

This will start the reboot process. Wait 15-30 seconds before using the server. All processes will automatically reload.

## Password

The default password for a fresh install is: __cloudadmin__.

!!! warning
    You should change the default password after the install.
    
Log in with the __coronium__ user:

```
ssh coronium@<your-instance-ip>
```

Use the following command to change the password:

```
sudo passwd coronium
```

And then follow the prompts.

## Databases

You will need to run the database setup tool so that you can connect to your databases, both internally, and externally.

1\. If you're not logged in, then log in with the __coronium__ user:

```
ssh coronium@<your-instance-ip>
```

2\. Enter your password. If you have not updated the password yet, the default is __cloudadmin__.

3\. Run the database setup tool:

```
sudo coronium-dbsetup
```

4\. Carefully follow the prompts to set up the passwords for both MySQL and MongoDB.

5\. When you are finished, close the shell connection:

```
exit
```

!!! tip
    Use the passwords you set using the database tool to access your databases externally.

## Enable HTTPS

To secure your Coronium Core server with HTTPS, see the __[HTTPS/SSL](/ssl)__ section.
