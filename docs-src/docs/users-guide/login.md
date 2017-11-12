Once a user has been registered and confirmed in the system, you can log them in by providing the username and password to the client-side User module __[login](/client-module/users/#login)__ method.

A "login" provides you with the users unique identifier and whatever meta data you may have saved along with it, allowing you to work with the user. In addition, a login marks a special login event to help track your application logins per user, which can be viewed in the __[Webmin user section](/webmin-guide/users/)__.

The login functionality itself does nothing special as far as setting any type of "token" or similar. This is something that must be handled by the developer. Because of this, there is no special "logout" functionality. You as the developer determine when a user is logged out of your application.

The client-side User module also has a __[get]()__ method, which will retrieve all of the users stored data based on their unique user id (which can be obtained by the __[login]()__ method). When using the __[get]()__ method, no login tracking is performed. If you wish to track when a user is accessing your application, you must use the __[login]()__ method.