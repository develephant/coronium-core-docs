The default password for a fresh install is: __coroniumadmin__.

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

When you are finished, close the connection:

```
exit
```