```lua
--/jobs/myapp/myjob.lua
local job = core.job()

function job.run()

  core.log("I'm a Job!")

  return job.OK
end

return job
```