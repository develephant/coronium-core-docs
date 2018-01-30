!!! tldr "Template Storage"
    Server-side templates are stored in the __/home/coronium/templates__ directory. They have no relation to the __[Pages](/server/modules/pages/api/)__ templates.

For detailed information on how to work with templates, see the __[Templates](/server/modules/pages/templates/)__ section of the Pages guide.


### render

Render a template based on a passed key/value table. Returns __String__.

```lua
core.template.render(tpl_file, tpl_data)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tpl_file|The template file to render relative to the __templates__ directory.|_String_|__Y__|
|tpl_data|A table of key/value pairs to populate the template file.|_Table_|__Y__|

__Example__

```lua
local compiled_str = core.template.render('common_resp.tpl', {
  name = "Jenny",
  age = 23
})
```