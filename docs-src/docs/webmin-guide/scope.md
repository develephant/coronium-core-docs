!!! tip "Screencast Available"
    Learn about application scopes in a screencast format by __[Clicking here](/screencasts/#application-scopes)__.

Coronium Core supports multiple applications. Each Coronium Core based Corona application must have an "application scope" to allow for individual users and metrics. This allows you to filter users and metrics per application.

The application scope is set in the __[core.init](/client-module/core/#init)__ method on the client-side in your Corona application.

_It is important to choose a short but descriptive application scope name that is unique from your other application scopes._

!!! danger "Don't Change The Scope!"
    __Once the scope is set, do not change it for that particular application. If you do, you will lose user and metric associations.__

__Example__

```lua
core.init({
  server = "http://192.168.71.73",
  key = "03624656-ca90-11e7-b8d4-fb59abeb4c03",
  scope = "Space Race"
})
```

Once you have set the scope, all __[Users](/client-module/users/)__ and __[Analytics](/client-module/analytics/)__ module methods will be scoped to that specfic application, which you can use to filter views in the Coronium Core Webmin.

!!! note "Webmin Scope"
    You must run at least one User or Analytics method for the scope to be available in the Webmin. If the scope is not visible from the "Application Scope" dropdown menu, refresh the Webmin in your browser.