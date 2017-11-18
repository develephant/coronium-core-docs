A server key is generated the __/home/coronium/config/keyfile__ file when you first install the server. You use this key when connecting with the client-side plugin.

___Do not remove this file___. You should back up this file to your computer and store it some place safe.

__Viewing the Key__

While logged into the server as the __coronium__ user, enter the following on the command line:

```
coronium key
```

!!! tip "Post-Install"
    The server key is also displayed directly after installation in the console.

In the event that you need to generate a new keyfile, you can run __sudo coronium genkey__ on the command line.

!!! warning "Important Note"
    If you regenerate the keyfile, any deployed Corona apps that use the server key will be unable to connect to the server.