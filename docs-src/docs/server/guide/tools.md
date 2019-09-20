
## Command Line Tools

The __Coronium Core__ server command line tools can be used once you have logged into the server using your command line. Be sure to log in as the __coronium__ user.

### coronium

__Usage:__ `coronium`

Will show the possible options you can use with the `coronium` tool.

---

### mongo-updatepw

__Usage:__ `sudo mongo-updatepw`

Will display the Mongo password update tool.

---

### mongo-remote

__Usage:__ `sudo mongo-remote [on|off]`

Toggle the ability to connect to the Mongo database remotely.

---

### mongo-enabled

__Usage:__ `sudo mongo-enabled [true|false]`

Enable/disable the MongoDB service for the server. This will save on resources if you are not using MongoDB.

---

### mysql-updatepw

__Usage:__ `sudo mysql-updatepw`

Will display the MySQL password update tool.

---

### mysql-remote

__Usage:__ `sudo mysql-remote [on|off]`

Toggle the ability to connect to the MySQL database remotely.

---

### webmin-passwd

__Usage:__ `sudo webmin-passwd`

Will display the Webmin password update tool.

---

### webmin-apihost

!!! danger "Danger!"
    __Do not use this command unless you know why and how.__

__Usage:__ `sudo webmin-apihost <host-address>`

Change the API host address. 