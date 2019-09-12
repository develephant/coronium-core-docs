## Password

The default password for a fresh install is: __cloudadmin__.

!!! danger "Change The Password"
    __You should change the default password after the install.__
    
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

To secure your Coronium Core server with HTTPS, see the __[HTTPS/SSL](/server/guide/ssl/)__ section.

## Webmin Access

To access the Coronium Core Webmin, see the __[Webmin Guide](/server/webmin/setup/)__.

## Database Security

To keep your databases more secure see __[MySQL Remote Access](/server/modules/mysql/#remote-access)__ and __[MongoDB Remote Access](/server/modules/mongo/#remote-access)__.