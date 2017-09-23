It's a good idea to keep your Coronium Core server up to date with the latest release. To update your server, simply follow the directions below.

## Check your version

Using the [Coronium Tool](/server-guide/#coronium-tool), check the current version by running `coronium status` on the command line.

## Log in as root

You must log in as the __root__ user to perform an update on the server.

## Select an update

Listed below are one-liners specific to the versions for updating. Make sure to select the proper update based on your current version.

!!! fail "Important Note"
    You must be logged in as the __root__ user to run the updater or it may fail.

### 2.0.2 to 2.0.3

The following will update your Coronium Core 2.0.2 server to version 2.0.3

__Fixed__

- Temp cache body file now works properly for large payloads.

__Added__

- Pages `response` method returns a status properly when provided.
- Pages `status` convienence method added to quickly return a status code other than 200.
- Support for OPTIONS method for CORS support in Pages. 
- Server `coronium.body.conf` file which can be used to adjust the body cache for servers with large memory allocations.

Updater One-Liner

```
wget https://s3.amazonaws.com/coronium-core-update/v2.0.3/update.sh && sudo bash ./update.sh
```