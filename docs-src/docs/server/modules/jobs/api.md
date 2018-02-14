A cron-like service allowing you to run code periodically for a variety of use cases. 

See the __[Jobs Guide](/server/modules/jobs/guide/)__ section for implementation details.

## Job Objects

### core.job

Get a new reference to a Job object.

```lua
local job = core.job()
```

__Returns__

A new Job object to add your job code to.

__Example__

See the __[Jobs Guide](/server/modules/jobs/guide/)__ section for examples.

### job.run

The function that is used to run your job code.

```lua
function job.run()
  ...
end
```

__Example__

See the __[Jobs Guide](/server/modules/jobs/guide/)__ section for examples.

## Return

When your work is done in your Job file, you ___must___ return a response to the __Job Service__. It can be one of the following:

### job.OK

Lets the __Job Service__ know that this job has completed successfully, and should continue to run on the next service interval.

__Example__

```lua
return job.OK
```

### job.SLEEP

Lets the __Job Service__ know that this job has completed successfully, and should not run again for another 24 hours from its last run. This return is helpful for Jobs that use __[Helpers](#helpers)__

__Example__

```lua
return job.SLEEP
```

### job.STOP

Lets the __Job Service__ know that the Job should no longer run until the Job Service is restarted or reloaded. Can be useful for error, or other stop conditions. This does _not_ mark the Job __deactivated__ (see `job.EXIT`).

__Example__

```lua
return job.STOP
```

### job.EXIT

Lets the __Job Service__ know that this job should be marked as __deactivated__, and no longer run on service intervals. You must reactivate this Job through the Webmin to have it be included in the Job Service on future restarts or reloads.

__Example__

```lua
return job.EXIT
```

## Errors

If a Job causes a critical error or the job file cannot be found, it will be removed from the current service intervals, and the Job will be marked as __deactivated__. The last error message recieved, if any, will be stored. You can view he last error by viewing the Job details in the __Jobs__ section of the Webmin.

## Helpers

You can use the following helpers to conditionally run your job code under specific circumstances.

### job.minute

The minute based on UTC time, pass `true` for local time. Should be used with the __[job.OK](#jobok)__ return type.

```lua
job.minute(localTime)
```

__Returns__

A minute __number__ between 00-59.

### job.hour

The numerical hour in 24 hour format in UTC, pass `true` for local time. Should be used with the __[job.OK](#jobok)__ return type.

```lua
job.hour(localTime)
```

__Returns__

A hour __number__ between 00-23.

### job.day

The abbreviated day name based on UTC, pass `true` for local time.

```lua
job.day(localTime)
```

__Returns__

One of `Mon`, `Tue`, `Wed`, `Thur`, `Fri`, `Sat`, or `Sun` as a __string__.

### job.dayNum

The numerical day of the week in UTC, pass `true` for local time.

```lua
job.dayNum(localTime)
```

__Returns__

The current day number from 1-7 as a __number__. Monday is 1.

### job.dayMonth

The numerical day of the month in UTC, pass `true` for local time.

```lua
job.dayMonth(localTime)
```

__Returns__

A __number__ value from 1-31.

### job.weekNum

The numerical week of the year in UTC, pass `true` for local time.

```lua
job.weekNum(localTime)
```

__Returns__

A __number__ value from 00-53, with the first Monday as the first day of week one.

### job.month

The abbreviated month name based on UTC, pass `true` for local time.

```lua
job.month(localTime)
```

__Returns__

One of `Jan`, `Feb`, `Mar`, `Apr`, `May`, `Jun`, `Jul`, `Aug`, `Sep`, `Nov`, `Dec` as a __string__.

### job.monthNum

The numerical month number in UTC, pass `true` for local time.

```lua
job.monthNum(localTime)
```

__Returns__

The month between 1-12 as a __number__.
