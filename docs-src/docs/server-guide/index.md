## Webmin

Visualize your Coronium Core server, and create custom server APIs using your web browser. Learn more about the Coronium Core Webmin UI in the __[Webmin Guide](/webmin-guide/setup/)__.

## Coronium Tool

A command line tool is available on the server to handle a few common operations. To see available options, on the command line, type:

```
coronium
```

...and press the Enter key.

!!! note ""
    You must be logged in as the __coronium__ user to use the command line tool.

## Server Key

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

## Viewing Logs

To view log files, connect to the server with the __coronium__ user.

```
ssh coronium@<your-instance-ip>
```

!!! info "Viewing the Log"
    To quickly monitor the debug (nginx) log file, enter `cclog` on the command line. You can also view the debug log in the Webmin in the "debug log" section.

Log files can be found in the __/usr/local/coronium/logs__ directory. Possible log files include _nginx.log_ and _mongo.log_.

To watch a log file in real-time:

```
tail -f /usr/local/coronium/logs/<log-name>.log
```

Press __control-x__ to stop watching the log file.

!!! note ""
    The log files are managed automatically, and will be "rotated" once they exceed a certain size limit.

!!! tip
    To view all logs consolidated into one file (including the mysql.log) type __coronium logs__.

## Lua Code Cache

During production you should turn on the Lua code cache. You can do this using the Coronium Tool. First, log in as the __coronium__ user.

_Cache on_

```
sudo coronium cache on
```

_Cache off_

```
sudo coronium cache off
```

!!! tip
    During api development make sure to turn the Lua cache __off__ to see your changes.

## System Services

When your __Coronium Core__ server starts, its monitored by a utility called __Monit__, which makes sure that the required processes stay active. In the event that a process runs into an issue or crashes, it will be restarted shortly.

In the rare case where you need to manually stop, start or restart the Coronium stack, log in using the __coronium__ user.

```
ssh coronium@<your-instance-ip>
```

To stop the Coronium stack, on the command line, enter:

```
sudo coronium stop
```

To start the Coronium stack, use:

```
sudo coronium start
```

To restart the Coronium stack, use:

```
sudo coronium restart
```

!!! caution ""
    You should rarely need to manually control the Coronium stack process.


## User Directories

The following user directories can be found in __/home/coronium__. They are will not be affected in any server updates.

|Name|Description|See also|
|----|-----------|--------|
|files|Holds all file uploads.|[Files](/server-modules/files/)|
|projects|Holds all the api projects.|[Projects](/server-modules/api/#projects)|
|pages|Holds all public facing web pages.|[Pages](/pages-guide/api/)|

### Public Directory

If you store files in the __files/public__ directory they are accessible via the browser, which makes them insecure by nature. A common use case for storing files in the public directory is for displaying them within [page templates](/pages-guide/templates/). 

!!! note ""
    If a file with the same path exists in the __pages__ directory, it will be served first. If a file does not exist at the path, then the __files/public__ directory will be checked.

__Path Examples__

File: _/home/coronium/files/public/imgs/image.png_

URL: _https://your.coronium.server/imgs/image.png_

## Database Passwords

If you need to change the databases passwords at any time, you can issue the following commands, and carefully follow the instructions while logged in as the __coronium__ user:

### Mongo

You will need to provide your current Mongo password.

```
sudo mongo-updatepw
```

### MySQL

```
sudo mysql-updatepw
```

