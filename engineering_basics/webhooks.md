- [twilio article](https://sendgrid.com/blog/whats-webhook/)
- [github article](https://developer.github.com/webhooks/)

**Webhooks** are a way for one app to provide _real time information_ to another app. You just need to **subscribe** to an app. When you subscribe to an app, you _tell the webhook which URL to send POST requests to_.
- disadvantage: it can be a little tricky to set up
- advantage: you don't have to poll data frequently
- webhooks are principally ascynchronous
- you can mock requests using cURL/postman
- an easy way to attack a client it to send it POST requests that mimic the webhook's requests. Handle this using auth tokens.

### Example: Github
You can configure github to send an http post request to any URL you want. These requests will be triggered by events (like new PRs)
