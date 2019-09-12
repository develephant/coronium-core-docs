The Coronium Core server works much like a regular web server.

## Request

A request is sent by the client, either from the __[Corona plug-in](https://marketplace.coronalabs.com/plugin/coronium-core)__ or the __[Pages API](/server/modules/pages/api/)__, and then a client connection is initiated and the workload is processed by one of the built-in module APIs, or a custom server-side API.

As a developer you should make sure your server-side APIs are concise and optimized to get the response back to the client as quick as possible.

Every request takes up a server connection (and memory) until a response is returned.

## Response

Once the workload has been process, the response is sent back down to the client, and the client connection is closed.

_No state is kept_. Once the response is sent, the server no longer has access to the client connection. 

## Context

There are two special cases that work outside of the request/response lifecycle; __[Jobs](/server/modules/jobs/guide/)__ and __[Timers](/server/modules/timer/)__.

Both of these live in their own context that is detached from the request/response lifecycle. When using __Jobs__ or __Timers__ you are not able to send responses to a client, as they don't exist in this context.

__Jobs__ run in their own service and can only be managed through the __[Webmin](/server/webmin/setup/)__. __Timers__ can be started during a client request, but are immeadiatly detached into their own context.
