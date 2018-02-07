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

!!! info "Availability Notice"
    This version/update is no longer supported, but is available by request.

__Fixed__

- Temp cache body file now works properly for large payloads.
- Pages `response` method returns a status properly when provided.

__Added__

- Pages `status` convienence method added to quickly return a status code other than 200.
- Server `coronium.body.conf` file which can be used to adjust the body cache for servers with large memory allocations.

### 2.0.3 to 2.1.0

!!! info "Availability Notice"
    This version/update is no longer supported, but is available by request.

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

### 2.1.0 to 2.1.1

!!! info "Availability Notice"
    This version/update is no longer supported, but is available by request.

__Added__

- Full support for multiple applications with separate scope for users and metrics.
- Webmin "Today" view for global application usage metrics for the current day.

### 2.1.1 to 2.2.0

!!! info "Availability Notice"
    This version/update is no longer supported, but is available by request.

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

__DigitalOcean Updater__

!!! fail ""
    You must be logged in as the __root__ user to run the updater or it may fail. __This update requires a server reboot__.

Paste the following one-liner into your terminal to start the DigitalOcean update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.3.0/do/update.sh && sudo bash ./update.sh
```

__Amazon EC2 Updater__

!!! fail ""
    You must be logged in as the __ubuntu__ user to run the updater or it may fail. __This update requires a server reboot__.

Paste the following one-liner into your terminal to start the Amazon EC2 update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.3.0/ec2/update.sh && sudo bash ./update.sh
```

### 2.3.0 to 2.3.2

The following will update your Coronium Core 2.3.0 or 2.3.1 server to version 2.3.2

__Fixed__

  - Locked down all module access to the administration database.
  - Provided enhanced security for client-side MySQL injections.

__Updated__

  - __core.users__ `extra` storage increased significantly.

<i class="fab fa-digital-ocean"></i> __DigitalOcean Updater__

!!! fail ""
    You must be logged in as the __root__ user to run the updater or it may fail. _A server reboot is recommended_.

Paste the following one-liner into your terminal to start the DigitalOcean update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.3.2/do/update.sh && sudo bash ./update.sh
```

<i class="fab fa-amazon"></i> __Amazon EC2 Updater__

!!! fail ""
    You must be logged in as the __ubuntu__ user to run the updater or it may fail. _A server reboot is recommended_.

Paste the following one-liner into your terminal to start the Amazon EC2 update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.3.2/ec2/update.sh && sudo bash ./update.sh
```

### 2.3.2 to 2.4.0

The following will update your Coronium Core 2.3.2 server to version 2.4.0

__Added__

  - Server-side __[Users](/server/modules/users/api/)__ module, allowing for custom user logic and types. Includes new methods __[getGroup](/server/modules/users/api/#getgroup)__, __[getWithQuery](/server/modules/users/api/#getwithquery)__, and __[getAndMerge](/server/modules/users/api/#getandmerge)__ for advanced user queries, record associations, and more.
  
  - Server-side and client-side __[OAuth API](/server/modules/users/oauth/)__ which is a __Users__ module extension to support OAuth client IDs, access tokens, and expirys from Facebook login, Google sign-in, etc.

  - MySQL module EZ Query method __mysql.selectMerge__ to select from multiple databases and tables in a single call. See the client-side __[selectMerge](/client/modules/mysql/#selectmerge)__ or server-side __[selectMerge](/server/modules/mysql/#selectmerge)__ documentation for more details. 

  - MySQL module EZ Query method __mysql.selectCount__ to get records counts based on queries. See the client-side __[selectCount](/client/modules/mysql/#selectcount)__ or server-side __[selectCount](/server/modules/mysql/#selectcount)__ documentation for more details. 

  - Server-side MySQL module __[time based methods](/server/modules/mysql/#timestamp)__ can now accept a UNIX timestamp for conversion.

  - Server-side MySQL module __[logQueries](/server/modules/mysql/#logqueries)__ method to enable logging of the raw queries being created under the hood for debugging purposes.

  - Modules that accept a `where` key can now benefit from a new special table type clause. See __[The WHERE Key](/server/modules/mysql/#the-where-key)__ for more information.

  - A new field in the __Users__ module called `group` to help partition your users within a scope.

  - A new incoming parameter `scope` for use in custom server-side API methods (see __[Input](/server/modules/api/#input)__).

  - Logging is now split into _api_, _pages_, and _nginx_ logs for less noise and more specificality.

  - Lots of new and revised documentation.

__Fixed__

  - Users module `Field 'extra' doesn't have a default value` error.

  - Administration database collation type inconsistencies.

  - Trigger privilege unavailable for external MySQL admin clients.

__Updated__

  - Client connection count increased significantly.

  - Almost all server-side methods now support error, and error status codes that can be returned to the client. See __[Status Codes](/client/codes/)__.

__API Changes__

  - __mysql.selectBatch__ `key` parameter is now optional per entry. If not specified the table name will be used instead. Be sure to provide a `key` if multiple entries use the same table.

  - __mysql.selectBatch__ now returns a single record (similar to __mysql.selectOne__) if the `limit` for an entry is set to 1.

__Webmin Update 1.2__

  - Now supports multi-file server-side API projects.
  
  - New Webmin settings area in the __Config__ section.

  - Log viewer updated to view newly split log files. 

  - Log displays with (optional) colorized output. Increased line output.

  - Dark theme now available for the code editor.

  - New user keys `oauth` and `group` added to the user details page.

  - Filter by group added to the user search page.

  - Code editor completions for all new methods.

  - Lots of updates and fixes both under and over the hood.

<i class="fab fa-digital-ocean"></i> __DigitalOcean Updater__

!!! danger "Root User Required"
    You must be logged in as the __root__ user to run the updater or it may fail. __This update requires a server reboot__.

Paste the following one-liner into your terminal to start the DigitalOcean update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.4.0/do.sh && sudo bash do.sh
```

<i class="fab fa-amazon"></i> __Amazon EC2 Updater__

!!! danger "Ubuntu User Required"
    You must be logged in as the __ubuntu__ user to run the updater or it may fail. __This update requires a server reboot__.

Paste the following one-liner into your terminal to start the Amazon EC2 update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.4.0/ec2.sh && sudo bash ec2.sh
```

### 2.4.0 to 2.4.1

The following will update your Coronium Core 2.4.0 server to version 2.4.1

__Added__

  - Userland __nginx__ config directories for server add-ons (like PHP, etc).
  - Safety to make sure an update is applicable to the installed version.
  - _Using AJAX with the Pages API_ in the [documentation](/server/modules/pages/ajax/).

__Updated__

  - __Nginx__ config updated for better throughput at high loads.

__Webmin 1.3.0__

 - New __System__ section added to show general system information.
 - New version alert added to show when a new version is available.
 - Safety to make sure unsaved code is not lost before leaving the code editor.
 - Unsaved code is automatically saved if the editor is left idle for more than 60 seconds.
 - You can now download a .zip archive of your current API projects in the __System__ section.
 - Various cosmetic changes and updates.

<i class="fab fa-digital-ocean"></i> __DigitalOcean Updater__

!!! danger "Root User Required"
    You must be logged in as the __root__ user to run the updater or it may fail. _A server reboot is recommended_.

Paste the following one-liner into your terminal to start the DigitalOcean update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.4.1/do.sh && sudo bash do.sh
```

<i class="fab fa-amazon"></i> __Amazon EC2 Updater__

!!! danger "Ubuntu User Required"
    You must be logged in as the __ubuntu__ user to run the updater or it may fail. _A server reboot is recommended_.

Paste the following one-liner into your terminal to start the Amazon EC2 update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.4.1/ec2.sh && sudo bash ec2.sh
```

## Webmin HTTPS/SSL

If your Coronium Core install was setup with HTTPS/SSL support, you will need to update the Webmin API host to work over HTTPS/SSL as well.

!!! important
    If you've performed this step in a previous update, you __do not__ need to do it again, unless you are having problems accessing your Webmin.

Log in as the __coronium__ user, and from the command line, run the following, using your secure domain name:

```
sudo webmin-apihost https://<your.coronium.host>
```

