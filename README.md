# Strava Webhook Event API Endpoint

This repo provides an endpoint implementation in Lua for the [Strava Webhook Event API](http://strava.github.io/api/v3/events/). It uses the embedded Lua version for Nginx contained in [OpenResty](https://openresty.org/en/) as building this extension yourself is quite a hassle (be warned!).

**This is currently WIP, only the subscription part works for now.**

# Get the stuff up and running

* Create an application for the [Strava API](https://www.strava.com/settings/api).
* **Request access to the Webhook Event API from Strava.** Drop a friendly mail to developers -at- strava.com, provide the client ID of your Strava application and why you want access to that part of the API.
* Clone this project. Either run it as a Docker container using `docker-compose up -d` or deploy the Nginx configuration from `files/nginx.conf` in your own OpenResty server instance.
* Execute the following command to register a callback with the Strava API. Substitute the variables with your values. The client ID and the client secret can be obtained from the [Strava API](https://www.strava.com/settings/api) website.
```
CLIENT_SECRET='your-client-secret'
CLIENT_ID='your-client-id'
WEBSERVER='hostname-of-your-webserver'

curl -X POST https://api.strava.com/api/v3/push_subscriptions \
  -F client_id=${CLIENT_ID} \
  -F client_secret=${CLIENT_SECRET} \
  -F 'object_type=activity' \
  -F 'aspect_type=create' \
  -F 'callback_url=https://${WEBSERVER}/strava' \
  -F 'verify_token=STRAVA'
 ```
 Your access log should show a request from Strava containing a *hub challenge*:
 ```
 127.0.0.1 - - [28/Jun/2017:17:34:13 +0000] "GET /strava?hub.challenge=abcd1234&hub.mode=subscribe&hub.verify_token=STRAVA HTTP/1.1" 200 134 "-" "Go-http-client/1.1"
 ```
 
 If everything works as expected you should get a reply like this:
 ```
 {
   "id":424242,
   "resource_state":2,
   "application_id":9999,
   "aspect_type":"create",
   "object_type":"activity",
   "callback_url":"https://your-webserver.cx/strava",
   "created_at":"2017-06-28T17:33:36.308171157Z",
   "updated_at":"2017-06-28T17:33:36.308170585Z"
 }
 ```
 
 * Create a new activity on Strava and watch the access of your OpenResty instance for a callback from Strava. You should see something like
 ```
 127.0.0.1 - - [28/Jun/2017:17:41:41 +0000] "POST /strava HTTP/1.1" 200 121 "-" "Go-http-client/1.1"
 ```
 in the logs.
 
 
 
 # Todo
 
 * handle updates (e.g. new activities) from Strava
