A server key is generated when you first install the server. You use this key when connecting with the client-side plugin. See the client-side __[Initialization](/client/guide/#initialization)__ section for more details.

## Viewing Your Key

Using the __[Webmin](/server/webmin/setup/)__ you can find the server key in the __Configuration__ section.

!!! info "Post-Install"
    The server key is also displayed directly after installation in the console.

## Regenerate A Key

In the event that you need to generate a new keyfile, log in as the __coronium__ user and on the command line run:

```text
sudo coronium genkey
```

!!! warning "Important Note"
    If you regenerate the keyfile, any deployed Corona apps that use the server key will be unable to connect to the server.