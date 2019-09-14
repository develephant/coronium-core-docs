If you don't plan on using any of the Mongo based modules, like __[core.data](/server/modules/data/)__ or __[core.mongo](/server/modules/mongo/)__ you can reclaim some memory and resources for your system.

To disable MongoDB, you will need to log in as the __root__ user (__ubuntu__ on EC2) and run the following on the command line:

```sh
sudo mongo-enabled false
``` 

This will stop the MongoDB service from running. ___The server will automatically reboot after issuing this command___.

In the future, if you decide you need MongoDB, log in as the __root__ user (__ubuntu__ on EC2), and run:

```sh
sudo mongo-enabled true
```