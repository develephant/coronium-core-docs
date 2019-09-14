## MySQL

### Administration

To manage the MySQL database installed on your Coronium Core server, open the Webmin and click "Databases" under the tools sections on the left navigation bar.

Next, click "Open MySQL Manager" on the Database Admin page and enter `coronium` as the user and the password you set up for your MySQL database during installation.

### Configuration File

You can adjust the various MySQL server configuration settings in the _coronium_mysql.cnf_ file. The file is located at:

```bash
/etc/mysql/coronium.conf.d/coronium_mysql.cnf
```

!!! danger "Configuration Changes"
    __Changes to the configuration file can have adverse effects, do so at your own risk. See [MySQL Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) for the available configuration options.__

This file can be edited by using the __nano__ utility on the server:

```bash
sudo nano /etc/mysql/coronium.conf.d/coronium_mysql.cnf
```

Use the arrow keys on your keyboard to move the cursor. When your updates are complete, use __ctrl-x__ and then press __y__ to save the file.

You will need to restart the MySQL server daemon for the changes to take effect:

```bash
sudo monit restart mysqld
```

Check the Coronium server status to make sure that MySQL is running:

```lua
coronium status
```

### Viewing Error Log

The MySQL server error log can be viewed using:

```bash
sudo tail -f /var/log/mysql/error.log
```

Use __ctrl-c__ to exit.

### Password Update

To change your MySQL password log in your Coronium Core server as and run:

```sh
sudo mysql-updatepw
```

## MongoDB

### Administration

To manage the MongoDB database installed on your Coronium Core server, open the Webmin and click "Databases" under the tools sections on the left navigation bar.

Next, click "Open MongoDB Manager" on the Database Admin page and enter `coronium` as the user and the password you set up for your MongoDB database during installation.

### Password Update

To change your Mongo password log in your Coronium Core server and run:

```sh
sudo mongo-updatepw
```

### Disable MongoDB

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