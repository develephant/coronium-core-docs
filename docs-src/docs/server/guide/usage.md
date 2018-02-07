## Coronium Webmin

Use the Coronium __[Webmin](/server/webmin/setup/)__ to view and control many aspects of your Coronium Core server through a web browser.

## Coronium Tool

The Coronium tool is accessable via SSH. You must be logged in as the __coronium__ user to use the command line tool.

A command line tool is available on the server to handle a few common operations. To see available options, on the command line, type:

```
coronium
```

...and press the Enter key.

## Viewing Logs

### Webmin

Using the __[Webmin](/server/webmin/setup/)__ go to the __Log Viewer__ section and select a log to view.

### SSH

To view log files via SSH, connect to the server with the __coronium__ user.

```
ssh coronium@<your-instance-ip>
```

To quickly monitor the debug log file, enter `cclog` on the command line.

Log files can be found in the __/usr/local/coronium/logs__ directory.

To watch a log file in real-time:

```
tail -f /usr/local/coronium/logs/<log-name>.log
```

Press __control-x__ to stop watching the log file.

!!! note "Log File Rotation"
    The log files are managed automatically, and will be "rotated" once they exceed a certain size limit.

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

!!! caution
    You should rarely need to manually control the Coronium stack process.


## User Directories

The following user directories can be found in __/home/coronium__. They are will not be affected in any server updates.

|Name|Description|See also|
|----|-----------|--------|
|files|Holds all file uploads.|[Files](/server/modules/files/)|
|projects|Holds all the api projects.|[Projects](/server/modules/api/#projects)|
|pages|Holds all public facing web pages.|[Pages](/server/modules/pages/api/)|
|templates|Holds all templates for the server-side template module.|[Template](/server/modules/template/)|
|config|Holds all configuration files.||

### Public Directory

If you store files in the __files/public__ directory they are accessible via the browser, which makes them insecure by nature. A common use case for storing files in the public directory is for displaying them within [page templates](/server/modules/pages/templates/). 

!!! tldr "File Selection"
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

