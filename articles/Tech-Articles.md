# Tech Articles
Here are some articles that I want to read or have read that are related to tech

## To Read

## Have Read
[A million notifications an hour](https://blog.gojekengineering.com/how-we-manage-a-million-push-notifications-an-hour-549a1e3ca2c2)
- pushing notifications can get complicated really fast. You can have multiple services, and users with multiple devices. Also, each device has a different notification system.
- The solution is to identify what you need to do, then break the system down into components as small as possible. In the case of this company, they used:
  - A notification server - services that want to send a notification hit this service. It pushes the job onto the job queue
  - job queue (rabbitMQ) - there are separate queues for each type of job. This helps mitigate problems. If there's a problem for one service, it won't affect any of the others
  - A token store - helps keep track of users who are logged in. When a user logs out, their authentication token gets deleted
  - A notification worker - Consume jobs from the job queue. Takes those jobs, and pushes them to the third-party notification providers