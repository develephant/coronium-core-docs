It's a good idea to keep your Coronium Core server up to date with the latest release. To update your server, simply follow the directions below.

## Check your version

Using the [Coronium Tool](/server/guide/usage/#coronium-tool), check the current version by running `coronium status` on the command line.

!!! tip "Webmin"
    You can also check the version using the __[Webmin](/server/webmin/setup/)__ by viewing the "config" section.

## Log in as root

You must log in as the __root__ user to perform an update on the server.

## Select an update

Listed below are one-liners specific to the versions for updating. Make sure to select the proper update based on your current version.

### 2.0.2 to 2.0.3

!!! fail "Important Note"
    You must be logged in as the __root__ user to run the updater or it may fail.

The following will update your Coronium Core 2.0.2 server to version 2.0.3

__Fixed__

- Temp cache body file now works properly for large payloads.
- Pages `response` method returns a status properly when provided.

__Added__

- Pages `status` convienence method added to quickly return a status code other than 200.
- Server `coronium.body.conf` file which can be used to adjust the body cache for servers with large memory allocations.

Paste the following one-liner into your terminal to start the update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.0.3/update.sh && sudo bash ./update.sh
```

__When the update is finished reboot the server using `sudo reboot -h`.__

Once the reboot has finished you can log in with the __coronium__ user to verify the server is updated by running the `coronium status` command.

### 2.0.3 to 2.1.0

!!! fail "Important Note"
    You must be logged in as the __root__ user to run the updater or it may fail.

The following will update your Coronium Core 2.0.3 server to version 2.1.0

__Added__

- Browser based web admin for informational data in regards to app usage, users, device metrics, minor administration, and more.
- Browser based code editor for building server-side project APIs.
- Client-side Users module for user registration, login tracking, and optional customizable email confirmations.
- Client-side Analytics module to collect usage statistics and device metrics.
- Server-side Email module for sending email via Mailgun.
- Server-side Template module for resuable customized output.
- CORS support for the Pages module.
- MySQL EZ Query method `selectOne` added for simple single record retrieval.
- MySQL EZ Query method `insertMany` added for optimized multi-record insertion.
- Various date and time utility methods for the MySQL module.
- Ability to adjust Corona network client-side timeout for large workloads.
- Ability to adjust MySQL server-side timeout for large workloads.

#### DigitalOcean

Paste the following one-liner into your terminal to start the DigitalOcean update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.1.0/do/update.sh && sudo bash ./update.sh
```

#### Amazon EC2

_Coming Soon_

### 2.1.0 to 2.1.1

!!! fail "Important Note"
    You must be logged in as the __root__ user to run the updater or it may fail.

The following will update your Coronium Core 2.1.0 server to version 2.1.1

__Added__

- Full support for multiple applications with separate scope for users and metrics.
- Webmin "Today" view for global application usage metrics for the current day.

#### DigitalOcean

Paste the following one-liner into your terminal to start the DigitalOcean update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.1.1/do/update.sh && sudo bash ./update.sh
```

#### Amazon EC2

_Coming Soon_

### 2.1.1 to 2.2.0

!!! fail "Important Note"
    You must be logged in as the __root__ user to run the updater or it may fail.

The following will update your Coronium Core 2.1.1 server to version 2.2.0

__Added__

- __[resendConfirmation](/client/modules/users/api/#resendconfirmation)__ method added to the Users module, to resend a confirmation email.

- Users "extra" metadata now supports boolean types.

__Fixed__

- Documentation links in the Webmin now resolve properly.

__API Changes__

`users.login`

The client-side Users module __[login](/client/modules/users/api/#login)__ response has changed. See the __[Logging In](/client/modules/users/login/)__ section of the documentation for more information.

`users.create`

The client-side Users module __[create](/client/modules/users/api/#create)__ response has changed. See the __[Creating](/client/modules/users/creating/)__ users section of the documentation for more information.

!!! warning
    Do not update your Coronium Core server to this version until you have updated any code to reflect the new changes.

#### DigitalOcean

Paste the following one-liner into your terminal to start the DigitalOcean update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.2.0/do/update.sh && sudo bash ./update.sh
```

#### Amazon EC2

_Coming Soon_

### 2.2.0 to 2.3.0

The following will update your Coronium Core 2.2.0 server to version 2.3.0

__Added__

  - The EZ Query MySQL module methods __selectBatch__, __insertBatch__, __updateMany__, __updateBatch__, __deleteMany__, and __deleteBatch__ have been added for optimized performance, reduced network calls, and simplification of code when working with multiple query entries; particularly on the client-side. See the client-side __[MySQL module](/client/modules/mysql/)__ documentation for more details. See also __[Optimized Methods](/client/modules/mysql/#optimized-methods)__.

  - The server-side MySQL module methods __dbConnect__, __dbQuery__, and __dbClose__ have been added for advanced control over the database connection, allowing for highly performant queries. See __[Advanced Methods](/server/modules/mysql/#advanced-methods)__ for more information.

  - The ability to fine tune the Coronium MySQL server configuration, which has been consolidated into a single file. See the __[Configruation File](/server/guide/mysql/#configuration-file)__ section in the MySQL server guide.

  - A client-side response event key named __tt__, which shows the total trip time for the request. See __[Response Events](/client/guide/#response-events)__ for more details.

  - A number of system-wide tunings for better performance for high traffic.

__Fixed__

  - The __insertMany__ MySQL module method now handles a single entry properly.

__Updated__

  - Nginx core updated to the latest version.
  - LuaJIT core updated to the latest version.
  - API request throttle has been slightly lowered.
  - Webmin code editor completions for new methods.

__API Changes__

  - The __insertMany__ MySQL module response has been changed. Previously the response returned the number of records inserted. Now the response contains an array of tables with either the inserted id or error. Previously this method would error out if _any_ of the inserts failed. Now all inserts are attempted and the error, if any, for the insert is returned in the response array. See the client-side __[insertMany](/client/modules/mysql/#insertmany)__ or server-side __[insertMany](/server/modules/mysql/#insertmany)__ documentation for more details.

#### DigitalOcean

!!! fail ""
    You must be logged in as the __root__ user to run the updater or it may fail. __This update requires a server reboot__.

Paste the following one-liner into your terminal to start the DigitalOcean update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.3.0/do/update.sh && sudo bash ./update.sh
```

#### Amazon EC2

!!! fail ""
    You must be logged in as the __ubuntu__ user to run the updater or it may fail. __This update requires a server reboot__.

Paste the following one-liner into your terminal to start the Amazon EC2 update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.3.0/ec2/update.sh && sudo bash ./update.sh
```

### 2.3.0 to 2.3.1

The following will update your Coronium Core 2.3.0 server to version 2.3.1

__Fixed__

  - Locked down all module access to the administration database.

__Updated__

  - __core.users__ `extra` storage increased significantly.

#### DigitalOcean

!!! fail ""
    You must be logged in as the __root__ user to run the updater or it may fail. _A server reboot is recommended_.

Paste the following one-liner into your terminal to start the DigitalOcean update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.3.1/do/update.sh && sudo bash ./update.sh
```

#### Amazon EC2

!!! fail ""
    You must be logged in as the __ubuntu__ user to run the updater or it may fail. _A server reboot is recommended_.

Paste the following one-liner into your terminal to start the Amazon EC2 update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.3.1/ec2/update.sh && sudo bash ./update.sh

## Webmin HTTPS/SSL

If your Coronium Core install was setup with HTTPS/SSL support, you will need to update the Webmin API host to work over HTTPS/SSL as well.

!!! important
    If you've performed this step in a previous update, you __do not__ need to do it again, unless you are having problems accessing your Webmin.

Log in as the __coronium__ user, and from the command line, run the following, using your secure domain name:

```
sudo webmin-apihost https://<your.coronium.host>
```

