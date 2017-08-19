When your __Coronium SkyTable__ server starts, its monitored by a utility called __Monit__, which makes sure that the required processes stay active. In the event that a process runs into an issue or crashes, it will be restarted shortly.

In the rare case where you need to manually stop or start the processes, log in using the __coronium__ user.

```
ssh coronium@<your-instance-ip>
```

To stop the processes, on the command line, enter:

```
sudo coronium stop
```

To start the processes, use:

```
sudo coronium start
```

!!! caution
    You should rarely need to manually control the processes.

