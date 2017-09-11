## Validation

To use Coronium Pages you will need to validate the server module against your Coronium Core plugin. This is a one-time process, and is done using a quick call through a Corona project.

### Create the script

First make sure your Coronium Core server is running, and then create a new Corona project with the [Coronium Core plugin](/client-setup/#get-the-plugin).

Add the following code to the __main.lua__, after the [initialzation](/client-guide/#initialization) code:

```lua
core.pages.generateAuth(function(evt)
  core.debug(evt)
end)
```

### Run the script

Run the Corona project. If all goes well, you should see __result: true__ output to the Corona console.

Your Pages module is now validated, you don't need to run this code again for this Coronium Core server instance.

!!! warning "Important"
    If you reinstall or move your __Coronium Core__ server you will need to revalidate.