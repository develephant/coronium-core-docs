# Viewing Logs

To view log files, connect to the server with the __coronium__ user.

```
ssh coronium@<your-instance-ip>
```

!!! tip
    To quickly monitor the main server log file, enter `cclog` on the command line.

Log files can be found in the __/usr/local/coronium/logs__ directory. Possible log files include _nginx.log_ and _mongo.log_.

To watch a log file in real-time:

```
tail -f /usr/local/coronium/logs/<log-name>.log
```

Press __control-x__ to stop watching the log file.

!!! note
    The log files are managed automatically, and will be "rotated" once they exceed a certain size limit.

!!! tip
    To view all logs consolidated into one file (including the mysql.log) type __cc logs__.

---

# System Services

When your __Coronium Core__ server starts, its monitored by a utility called __Monit__, which makes sure that the required processes stay active. In the event that a process runs into an issue or crashes, it will be restarted shortly.

In the rare case where you need to manually stop, start or restart the Coronium stack, log in using the __coronium__ user.

```
ssh coronium@<your-instance-ip>
```

To stop the Coronium stack, on the command line, enter:

```
sudo monit -g coronium stop
```

To start the Coronium stack, use:

```
sudo monit -g coronium start
```

To restart the Coronium stack:

```
sudo monit -g coronium restart
```

!!! caution
    You should rarely need to manually control the Coronium stack process.

## Upload Directory

All file uploads are stored in the ___/home/coronium/files___ directory.

# Coronium Tool

A command line tool is available on the server to handle a few common operations. To see available options, on the command line, type:

```
coronium
```

...and press the Enter key.

!!! note
    You must be logged in as the __coronium__ user to use the tool.