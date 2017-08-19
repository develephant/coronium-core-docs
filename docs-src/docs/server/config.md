After the __Coronium SkyTable__ server is running, you can change the server key which the plugin uses to connect to your instance.

Log in with the __coronium__ user:

```
ssh coronium@<your-instance-ip>
```

Enter your password. If you have not updated the password yet, the default is __coroniumadmin__.

Open the __config.lua__ using the __nano__ file editor:

```
nano ~/lib/coronium/config.lua
```

You should see the following content in the __config.lua__ file:

```
return 
{
  key = "ab34b95ef9cc8b024bd184"
}
```

Use the arrow keys to move the cursor, and replace the __key__ value, making sure to keep it enclosed in quotes:

```
return 
{
  key = "12345abcdefg"
}
```

When your changes have been made, use __control-x__ , then press __y__, and then press enter, to save the changes.

Reload the underlying __nginx__ process to pick up the new configuration:

```
sudo monit restart nginx
```

Close the shell connection with:

```
exit
```

!!! note
    The __key__ value will need to be added/updated in the plugin __init__ method as well. See __[Getting Started](/guide/#getting-started)__.