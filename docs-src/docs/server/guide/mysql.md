## Configuration File

You can adjust the various MySQL server configuration settings in the _coronium_mysql.cnf_ file. The file is located at:

```bash
/etc/mysql/coronium.conf.d/coronium_mysql.cnf
```

!!! warning
    Changes to the configuration file can have adverse effects, do so at your own risk. See [MySQL Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) for the available configuration options.

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

## Error Log

The MySQL server error log can be viewed using:

```bash
sudo tail -f /var/log/mysql/error.log
```

Use __ctrl-c__ to exit.