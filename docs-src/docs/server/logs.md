To view log files, connect to the server with the __coronium__ user.

```
ssh coronium@<your-instance-ip>
```

Log files can be found in the __logs__ directory.

To watch a log file in real-time:

```
tail -f ~/logs/<log-name>.log
```

Press __control-x__ to stop watching the log file.

!!! note
    The log files are managed automatically, and will be "rotated" once they exceed a certain size limit.