The server-side __[Jobs API](/server/modules/jobs/api/)__ and service allows you to run code periodically in the background for a variety of use cases. A number of different Job types can be scheduled.

By their nature, Jobs are imperfect due to the fact that the can only run when there are free cycles during the execution tasks the server is currently handling, like client requests.

For the most part Jobs run as close to their intervals as possible, with a variance of about 10 seconds in the worst case scenarios.

## Management

All Job management is done using the Coronium Core __[Webmin](/server/webmin/setup/)__ in the __Jobs__ section.

### Viewing Jobs

To view all current Jobs available to the Jobs Service, navigate to the __Jobs__ section. You will be presented with a list of Jobs, including information such as the current status, last run time, and other information.

### Creating Jobs

Jobs are created in the __Jobs__ section. To create a new Job, navigate to the __Jobs__ section and click the __New Job__ button. Enter the requested information in the Job creation form. There is ample help and validation feedback directly on the form.

When you are done inputing your Job information, click the __Create__ button. You will then be redirected to the Job code editor to create your Job code. See __[Writing Jobs](#writing-jobs)__.

#### Run Once

When creating a Job, you have the option of setting it to __Run Once__. This marks the Job so that it will only run one time on the booting of the Coronium Core server (after its initial interval). These can be useful for Jobs that only need to run once during the lifetime of the Coronium Core service. They will not run again on a service __[reload](#reloading-jobs)__.

### Editing Jobs

To edit an existing Job, navigate to the __Jobs__ section. On the Job listing, click any Job name to edit the Job using the form presented.

When you are done editing your Job, click the __Update__ button. See also __[Reloading Jobs](#reloading-jobs)__.

To edit your Job code see __[Editing Job Code](#editing-job-code)__.

### Removing Jobs

To remove a Job from the service, enter the Job editing mode (see __[Editing Jobs](#editing-jobs)__ above). Click the __Delete__ button, and then confirm the Job removal. 

<div style="color:Tomato; font-weight:bold"><i class="fas fa-bolt"></i> This is a destructive action, and cannot be undone.</div>

### Reloading Jobs

After adding or editing Job details, you will need to reload the Job service. In the __Jobs__ section, click the __Reload__ button to start the reload process.

You may need to wait for up to 60 seconds for the Job service to reload. While you're waiting, the service status will change to "Reloading". Once the reload process is complete, the status will return to "Running".

Jobs that have aleady been running will not have their interval reset. For example, let's assume a Job is set to run every 120 seconds, and a reload is issued at 60 seconds in. On reload, that Job will retain its remaining run time, in this case 60 seconds.

### Job Status

In the __Jobs__ section, you will see various status icons in the Job listing depending on how the Job was returned (see __[Job Returns](#job-returns)__ below).

The following table describes the various status icons, and the return that initiates them. In the Jobs listing, you can hover your mouse over any icon to see more information.

|Status Icon|Status Description|Return Type|
|----|------------------|-----------|
|<i style="color:LimeGreen" class="fas fa-check fa-lg"></i>|Is active and recurring or active and waiting to run.|__job.OK__|
|<i style="color:LimeGreen" class="fas fa-hourglass-end fa-lg"></i>|Is active and will run again 24 hours after its last run.|__job.SLEEP__|
|<i style="color:Gold" class="fas fa-sync-alt fa-lg"></i>|Is active but will _not_ run again until a reload event is issued.|__job.STOP__|
|<i style="color:Tomato" class="fas fa-ban fa-lg"></i>|Is _not_ active, and will _not_ run until reactivated manually.|__job.EXIT__|
|<i style="color:LimeGreen" class="fas fa-hourglass-start fa-lg"></i>|This Job has been marked as __Run Once__ but has _not_ run yet.|See __[Run Once](#run-once)__|
|<i style="color:Grey" class="fas fa-coffee fa-lg"></i>|This Job has __Run Once__ and will not run until a service reboot.|See __[Run Once](#run-once)__|

### Job Errors

If any critical errors occur, or the Job file cannot be found when a Job is run, it will automatically be set to a deactivated state and will not run again until manually reactivated and a __[reload](#reloading-jobs)__ is issued.

## Writing Jobs

Once you've created a Job (see __[]()__) you will be taken to the Job code editor to add your Job code.

__All core modules are available for use in Job files.__ It is not possible to return responses to clients from Jobs (see __[Lifecycle/Context](/server/guide/lifecycle/)__).

### Creating Job Code

Once you have created your Job, you will be taken to the code editor and presented with a Job template.

#### Job Template

```lua
local job = core.job()

function job.run()
  --== Place your job code here

  return job.OK
end

return job
```

All of your Job code must be placed in the `job.run()` method to be run. When your Job work code is finished you must then __return__ a valid Job status type (see below). 

<div style="color:Tomato; font-weight:bold"><i class="fas fa-bolt"></i> Failure to return a valid Job status will deactivate the Job when run.</div>

#### Job Returns

Job returns determine how your Job will operate on its next run. The most common return is __[job.OK]()__ which keeps a job running in a recurring fashion.

To understand what each return type does, see the __[Return](/server/modules/jobs/api/#return)__ section of the __Jobs API__.

#### Job Helpers

Helpers are a group of methods that return the current minute, hour, day, etc. to help fine tune when your Job runs. To learn more about each helper see the __[Helpers](/server/modules/jobs/api/#helpers)__ section of the __Jobs API__.

__Helper Example__

```lua
local job = core.job()

function job.run()

  if job.day() == "Fri" then
    core.log("Do some Friday work")
  end

  return job.SLEEP

end

return job
```

In the example above we use the __[job.SLEEP](/server/modules/jobs/api/#jobsleep)__ return since we only need to check for a day on a daily basis.

Because Job intervals are imperfect, some helper types work better with certain return types. For instance, you may have unpredictable results when using the __[job.minute](/server/modules/jobs/api/#jobminute)__ or __[job.hour](/server/modules/jobs/api/#jobhour)__ helper with the __[job.SLEEP](/server/modules/jobs/api/#jobsleep)__ return.

Instead create a recurring Job that runs at least every 30 seconds to check for __minute__ resolutions, or a Job every 30 minutes to check an __hour__ resolution. You would want to use the __[job.OK](/server/modules/jobs/api/#jobok)__ return in these situations.

### Editing Job Code

To edit existing Job code, navigate to the __Jobs__ section, and click any of the links in the __File Path__ column. This will take you to the Job code editor with the requested Job code loaded into the editor.

Once you've edited your code be sure to save it. While not required, you should issue a __[reload](#reloading-jobs)__ when finished.

## Example Code

After creating your Job (see __[Creating Jobs](#creating-jobs)__) you can regulate them by the return status in your code.

### Recurring

This Job will keep running at its set interval by returning __[job.OK](/server/modules/jobs/api/#jobok)__

```lua
local job = core.job()

function job.run()

  --check stock
  local bikes, err, code = core.mysql.selectOne("products", {
    tbl = "toys",
    columns = { "stock" },
    where = { type = "Red Bikes" }
  })

  --update status
  if bikes.stock == 0 then
    local res, err, code = core.mysql.update("products", {
      tbl = "toys",
      values = { out_of_stock = true },
      where = { id = bikes.id }
    })
  end

  return job.OK
end

return job
```

### Sleep Job

This Job will stop special sales on Sundays. Checks the day every 24 hours using __[job.SLEEP](/server/modules/jobs/api/#jobsleep)__.

```lua
local job = core.job()

function job.run()

  if job.day() == "Sun" then
    local res, err, code = core.mysql.update("shop", {
      tbl = "status",
      values = { no_specials = true },
      where = { no_specials = false }
    })
  end

  return job.SLEEP
end

return job
```

### Stop Job

This Job will send daily sales reports during January. And return __[job.STOP](/server/modules/jobs/api/#jobstop)__ otherwise.

```lua
local job = core.job()

function job.run()

  local res, err, code = core.mysql.selectOne("shop", {
    tbl = "status",
    columns = { sales_total },
    where = { month = "Jan" }
  })

  if job.month() == "Jan" then
    local msg = core.email.new()
    msg:to("boss@work.com")
    msg:from("job@work.com")
    msg:subject("January Daily")
    msg:body("Sales today were: $"..res.sales_total)
    msg:send()

    return job.SLEEP
  else
    return job.STOP
  end
end

return job
```

### Exit Job

This Job will deactivate returning __[job.EXIT](/server/modules/jobs/api/#jobexit)__ if an error occurs with the query.

```lua
local job = core.job()

function job.run()
  
  local res, err, code = core.mysql.selectOne("products", {
    tbl = "toys",
    where = { type = "blender" }
  })

  if not res then
    core.log(err)
    return job.EXIT
  else
    return job.OK
  end

end

return job
```

### Run Once Job

This Job will run once. The proper return type is __[job.OK](/server/modules/jobs/api/#jobok)__ for __[Run Once](#run-once)__ jobs.

```lua
local job = core.job()

function job.run()
  
  local config_tbl, err = core.network.getJson("https://some.json-config.com")

  local config, err = core.data('config')

  local id, err = config:save(config_tbl)

  return job.OK
end

return job
```


