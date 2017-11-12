## Password

The default password for a fresh install is: __cloudadmin__.

!!! warning "Important Note"
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

## Enable HTTPS

To secure your Coronium Core server with HTTPS, see the __[HTTPS/SSL](/ssl)__ section.

## Activate Webmin

To activate the Coronium Core Webmin, see the __[Webmin Guide](/webmin-guide/setup/)__ setup section.


