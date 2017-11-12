This client-side module has only a single method, but performs a number of useful actions when it is called.

!!! tip "Screencast Available"
    Learn about collecting metrics in a screencast format by __[Clicking here](/screencasts/#collecting-metrics)__.

When using the __[appOpened](#appopened)__ method (see below), a number of pieces of information are collected about the environment in which the application is running in. It also tracks each time the application is accessed per device.

!!! info "User Statistics"
    For per user accesses see the client-side __[Users](/client-module/users/)__ module.

### appOpened

Logs an "opened" event and auto-populates application meta data.

```lua
core.analytics.appOpened([listener])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|listener|An optional listener callback function.|_Function_|__N__|

__Collected Meta Data__

The __appOpened__ method automatically collects the following meta data and stores it in the __analytics__ database.

|Name|Description|Example|
|----|-----------|-------|
|name|The application name as reported by Corona|FunRun|
|country_code|The originating country code, if any.|US|
|model|The device model the application is running on.|iPhone|
|platform|The platform the application is running on.|ios|
|platform_ver|The platform version the application is running on.|10.12.6|
|version|The application version string as reported by Corona.|com.app.funrun|
|manufacturer|The device manufacturer the application is running on.|Apple|
|screen_h|The screen pixel height as reported by Corona.|480|
|screen_w|The screen pixel width as reported by Corona.|320|
|corona_build|The Corona build the application is running on.|2017.3126|
|added|The timestamp of when the application was opened in UTC format.|2017-01-01 12:00:00|

## Viewing Analytics

You can view both the access and device statistics in the __[Webmin](/webmin-guide/setup/)__.