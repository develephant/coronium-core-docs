It's a good idea to keep your Coronium Core server up to date with the latest release. To update your server, simply follow the directions below.

## Available Updates

### 2.6.5 to 2.7.0

The following will update your Coronium Core 2.6.5 server to version 2.7.0

__Changes__

  - External access to databases has been disabled. This is a major change for security purposes. Please use the new internal administration tools in the Webmin. See __[Databases](/server/webmin/databases/)__ for more details. 

  - PHP 7 is now installed by default. Please note that PHP usage is for internal use only by custom server-side APIs.

__Added__

  - PHP server-side module for running PHP scripts within custom server-side APIs. See the __[PHP module](/server/modules/php/)__ API for more details.

__Webmin 2.1.0__

  - Internal administration tools for __MySQL__ and __MongoDB__ now available in the dedicated "Databases" section. See __[Databases](/server/webmin/databases/)__ for more details.


<br/>

<i class="fab fa-digital-ocean"></i> __DigitalOcean Updater__

!!! danger "Root User Required"
    You must be logged in as the __root__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __DigitalOcean__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.7.0/do.sh && sudo bash do.sh
```

---

<i class="fab fa-amazon"></i> __Amazon EC2 Updater__

!!! danger "Ubuntu User Required"
    You must be logged in as the __ubuntu__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __Amazon EC2__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.7.0/ec2.sh && sudo bash ec2.sh
```

<i class="fas fa-exclamation-triangle" style="color: red;"></i> ___Amazon EC2 users, please see the [Critical Update Notes for 2.7.0](/server/updates/#270-update) before updating.___

<br/>

### 2.6.4 to 2.6.5

The following will update your Coronium Core 2.6.4 server to version 2.6.5

__Updated__

  - Pages module version 2 which now supports form based uploads and cookies for user sessions, as well as a number of new methods for response output. See the updated documentation __[here](/server/modules/pages/usage/)__ for more information.

__Fixed__

 - An issue with the Pages module where an incoming request to a Lua page that doesn't exist writes an error message to the _pages.log_. Missing Pages dynamic Lua files now respond with a proper 404.

<br/>

<i class="fab fa-digital-ocean"></i> __DigitalOcean Updater__

!!! danger "Root User Required"
    You must be logged in as the __root__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __DigitalOcean__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.5/do.sh && sudo bash do.sh
```

---

<i class="fab fa-amazon"></i> __Amazon EC2 Updater__

!!! danger "Ubuntu User Required"
    You must be logged in as the __ubuntu__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __Amazon EC2__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.5/ec2.sh && sudo bash ec2.sh
```

<br/>

### 2.6.3 to 2.6.4

The following will update your Coronium Core 2.6.3 server to version 2.6.4

__Added__

 - Remote access tools and information for MySQL and MongoDB enhanced database security. See __[MySQL Remote Access](/server/modules/mysql/#remote-access)__ and __[MongoDB Remote Access](/server/modules/mongo/#remote-access)__ for more details.

 - MySQL module __[parseTimestamp](/server/modules/mysql/#parsetimestamp)__ method to convert MySQL timestamp to Unix timestamp.

<br/>

<i class="fab fa-digital-ocean"></i> __DigitalOcean Updater__

!!! danger "Root User Required"
    You must be logged in as the __root__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __DigitalOcean__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.4/do.sh && sudo bash do.sh
```

---

<i class="fab fa-amazon"></i> __Amazon EC2 Updater__

!!! danger "Ubuntu User Required"
    You must be logged in as the __ubuntu__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __Amazon EC2__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.4/ec2.sh && sudo bash ec2.sh
```

<br/>

### 2.6.2 to 2.6.3

The following will update your Coronium Core 2.6.2 server to version 2.6.3

__Fixed__

 - Coronium Core system logs not rotating properly.

<br/>

<i class="fab fa-digital-ocean"></i> __DigitalOcean Updater__

!!! danger "Root User Required"
    You must be logged in as the __root__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __DigitalOcean__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.3/do.sh && sudo bash do.sh
```

---

<i class="fab fa-amazon"></i> __Amazon EC2 Updater__

!!! danger "Ubuntu User Required"
    You must be logged in as the __ubuntu__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __Amazon EC2__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.3/ec2.sh && sudo bash ec2.sh
```

<br/>

### 2.6.1 to 2.6.2

The following will update your Coronium Core 2.6.1 server to version 2.6.2

__Added__

 - Ability to host __[Corona HTML5](https://forums.coronalabs.com/forum/637-html5/)__ apps and games on your __Coronium Core__ server, and utilize the __CoroniumJS__ plugin to access custom [server-side APIs](/server/modules/api/). See the __[HTML5 Builds](/server/modules/pages/html5/)__ section in the _Pages_ documentation for more details.

<br/>

<i class="fab fa-digital-ocean"></i> __DigitalOcean Updater__

!!! danger "Root User Required"
    You must be logged in as the __root__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __DigitalOcean__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.2/do.sh && sudo bash do.sh
```

---

<i class="fab fa-amazon"></i> __Amazon EC2 Updater__

!!! danger "Ubuntu User Required"
    You must be logged in as the __ubuntu__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __Amazon EC2__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.2/ec2.sh && sudo bash ec2.sh
```

<br/>

### 2.6.0 to 2.6.1

The following will update your Coronium Core 2.6.0 server to version 2.6.1

__Added__

 - Customizable error pages for common HTTP error codes. See the [Error Pages](/server/modules/pages/errors/) section in the Pages documentation.

__Fixed__

 - An issue with the Pages module where an incoming request to an unsupported endpoint would write an error message to the _pages.log_, filling the log file with useless entries.

__Updated__

 - Monit control files to be compatible with latest version. This allows running some other Coronium solutions on the Coronium Core server as well, like [Coronium ChatterBox](https://develephant.github.io/coronium-chatterbox-docs/).

<br/>

<i class="fab fa-digital-ocean"></i> __DigitalOcean Updater__

!!! danger "Root User Required"
    You must be logged in as the __root__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __DigitalOcean__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.1/do.sh && sudo bash do.sh
```

---

<i class="fab fa-amazon"></i> __Amazon EC2 Updater__

!!! danger "Ubuntu User Required"
    You must be logged in as the __ubuntu__ user to run the updater or it may fail.

Paste the following one-liner into your terminal to start the __Amazon EC2__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.1/ec2.sh && sudo bash ec2.sh
```

<br/>

### 2.5.0 to 2.6.0

The following will update your Coronium Core 2.5.0 server to version 2.6.0

<div style="color:Tomato"><strong><i class="fas fa-exclamation-circle"></i> Be sure to read the <a href="#260-update">Critical Update Notes</a> before installing this update.</strong></div>

__Added__

 - Password reset link that can be sent to a user with an email address using the server-side __[users.sendPasswordResetLink](/server/modules/users/api/#sendpasswordresetlink)__ method, allowing a user to reset their password via online form. Supports custom email template messaging. See also the __[Password Reset](/server/modules/users/passwordreset/)__ guide. 

 - __[Scope Permissions](/client/guide/#scope-permissions)__ for additional security, allowing a developer to control what client-side actions are allowed.

 - Server-side __[users.sendConfirmationLink](/server/modules/users/api/#sendconfirmationlink)__ to send a confirmation email for server-side created users. See also the __[Confirmations](/server/modules/users/confirmations/)__ guide. 

 - Server-side utility method __[core.md5](/server/modules/utils/#md5)__ which creates an MD5 digest of the given string.

 - Server-side command line tool to __[disable/enable MongoDB](/server/guide/mongo/)__ to gain some additional system resources in the event a developer is not using any MongoDB related functionality.

 - A bunch of new and updated documentation.

__Updated__

 - Client-side __[users.login](/client/modules/users/api/#login)__ method to support login by __email__ _or_ __username__.

 - Server-side __[users.getWithQuery](/server/modules/users/api/#getwithquery)__ to query against a hashed password.

 - Coronium Core Corona Plugin to support version 2.6.0 or better servers.

__Fixed__

 - Archive projects action in the __System Info__ section not downloading.

 - Server-side __[users.getWithQuery](/server/modules/users/api/#getwithquery)__ only working with the `active` key set.

 - User confirmation email send incorrectly reporting "failed" status.

 - Various minor issues.

__API Changes__

  - The client-side __[core.init](/client/modules/core/#init)__ method now requires a `version` parameter. See __[Initialization](/client/guide/#initialization)__.

__Webmin 2.0.0__

- New __Scopes__ section for viewing, and managing [Scope Permissions](/client/guide/#scope-permissions).

- _Auto Scroll Log_ option added to the __Configuration__ section.

- Code completions updated for new methods.

- Tons of tweaks, fixes, reorganization, and visual updates

<div style="color:Tomato"><strong><i class="fas fa-exclamation-circle"></i> Be sure to read the <a href="#260-update">Critical Update Notes</a> before installing this update.</strong></div>

<br/>

_Note: Close any open Webmin browser windows before updating._

<i class="fab fa-digital-ocean"></i> __DigitalOcean Updater__

!!! danger "Root User Required"
    You must be logged in as the __root__ user to run the updater or it may fail. __This update requires a server reboot__.

Paste the following one-liner into your terminal to start the __DigitalOcean__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.0/do.sh && sudo bash do.sh
```

---

<i class="fab fa-amazon"></i> __Amazon EC2 Updater__

!!! danger "Ubuntu User Required"
    You must be logged in as the __ubuntu__ user to run the updater or it may fail. __This update requires a server reboot__.

Paste the following one-liner into your terminal to start the __Amazon EC2__ update:

```
wget https://s3.amazonaws.com/coronium-core-update/v2.6.0/ec2.sh && sudo bash ec2.sh
```

## Depreciated

### 2.4.1 to 2.5.0

!!! info "Availability Notice"
    This version/update is no longer supported, but is available by request.

__Added__

  - Server-side __[Jobs API](/server/modules/jobs/api/)__ and service allowing you to run code periodically in the background for a variety of use cases. See the __[Jobs Guide](/server/modules/jobs/guide/)__ for full usage information.

  - A server-side utility method __[core.sleep](/server/modules/utils/#sleep)__ that allows one to pause code execution for a duration of time. Useful for tight work loops.

  - Server-side __[core.timer](/server/modules/timer/)__ method for single-use or recurring timers.

  - Server-side __[network.pipeline](/server/modules/network/#pipeline)__ method added to make sequential network calls to a host.

  - Ability to set a MySQL column to a valid `NULL` value when using the WHERE table syntax (see [here](/server/modules/mysql/#mysql-null-type)).

  - Updated documentation for all new additions.

__Fixed__

  - An edge case that could cause a log file to stop loading properly in the Log Viewer.

  - __[mysql.selectCount](/server/modules/mysql/#selectcount)__ returning a string type as opposed to a number.

__API Changes__

  - Server-side __[network.get]()__ previously returned only the response body. It now correctly returns a response object similar to __[network.request]()__.

  - Server-side __[network.post]()__ previously returned only the response body. It now correctly returns a response object similar to __[network.request]()__.

__Webmin 1.4.0__

  - __[Jobs](/server/modules/jobs/api/)__ section has been added to create, manage, and edit Jobs and the Job Service.

  - You can now manage log files in the __System Info__ section.

  - Optional auto-reload added when viewing log files.

  - Server key moved back to the __Configuration__ section.

  - More clean up, tweaks, and fixes.

### 2.4.0 to 2.4.1

!!! info "Availability Notice"
    This version/update is no longer supported, but is available by request.

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

### 2.3.2 to 2.4.0

!!! info "Availability Notice"
    This version/update is no longer supported, but is available by request.

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

### 2.3.0 to 2.3.2

!!! info "Availability Notice"
    This version/update is no longer supported, but is available by request.

The following will update your Coronium Core 2.3.0 or 2.3.1 server to version 2.3.2

__Fixed__

  - Locked down all module access to the administration database.
  - Provided enhanced security for client-side MySQL injections.

__Updated__

  - __core.users__ `extra` storage increased significantly.

### 2.2.0 to 2.3.0

!!! info "Availability Notice"
    This version/update is no longer supported, but is available by request.

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

### 2.1.0 to 2.1.1

!!! info "Availability Notice"
    This version/update is no longer supported, but is available by request.

__Added__

- Full support for multiple applications with separate scope for users and metrics.
- Webmin "Today" view for global application usage metrics for the current day.

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


### 2.0.2 to 2.0.3

!!! info "Availability Notice"
    This version/update is no longer supported, but is available by request.

__Fixed__

- Temp cache body file now works properly for large payloads.
- Pages `response` method returns a status properly when provided.

__Added__

- Pages `status` convienence method added to quickly return a status code other than 200.
- Server `coronium.body.conf` file which can be used to adjust the body cache for servers with large memory allocations.

## Webmin HTTPS/SSL

If your Coronium Core install was setup with HTTPS/SSL support, you will need to update the Webmin API host to work over HTTPS/SSL as well.

!!! important
    If you've performed this step in a previous update, you __do not__ need to do it again, unless you are having problems accessing your Webmin.

Log in as the __coronium__ user, and from the command line, run the following, using your secure domain name:

```
sudo webmin-apihost https://<your.coronium.host>
```

## Critical Update Notes

### 2.6.0 Update

Coronium Core version 2.6.0 introduces some major architectural changes to the underlying system to lay the groundwork for some current and future enhancements.

If at all possible it is reccomended to [run a fresh install](/server/installation/digitalocean/) and import your data and projects. If this is not an option, be sure to carefully read the notes that follow so your update will go smoothly.

__Live Apps__

If you currently have live apps running on v2.5.0 you can still update your server, but you will be unable to use any of the new features until you redeploy your application with the new Coronium Core Plugin set to version 2 (see [core.init](/client/modules/core/#init)). All of the following information needs to be understood as well before updating.

__Users Data__

Version 2.6.0 now supports [logins by email address](/client/modules/users/api/#login). ___This means that no two users in the same scope can have the same email address in the Users system___.

If you currently have users in the same scope with the same email address, certain functionality like __[users.sendPasswordReset](/server/modules/users/api/#sendpasswordreset)__, as well as, logging in by email address, will not work properly.  Other validation issues will also arise. 

___You will need to clean up Users that have this issue before updating___. If you have questions on how to do this, visit the [support forum](https://forums.coronalabs.com/forum/643-coronium/).

__Scopes__

With the addition of [Scope Permissions](/client/guide/#scope-permissions), scopes are being more fully intergrated into the Webmin system. 

When you first update, you will need to visit the __Scopes__ section in the Webmin and click the __Update Cache__ button to import your current scopes. You will then be able to edit permissions on them, if needed.

More enhancements to scopes management are coming in future updates.

__Plugin Version__

Because the Corona plugin system does not support multiple plugin versions internally, you must now supply a `version` parameter to the client-side __[core.init](/client/modules/core/#init)__ method. 

For server features included in v2.6.0 or better, you must set the plugin version number to __2__.

```lua
core.init({
  version = 2,
  server = "https://<coronium-host-address>",
  key = "<coronium-server-key>",
  scope = "Space Race"
})
```

### 2.7.0 Update

Amazon EC2 users will need to update the security group settings to use the new internal database administration tools. For the best overall security please adjust your settings as follows:

__Remove Ports__

  - 27017
  - 3306

__Add Port__

  - 10888