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
 
 * Create a new activity on Strava and watch the access of your OpenResty instance for a callback from Strava.
 
 # Todo
 
 * handle updates (e.g. new activities) from Strava
