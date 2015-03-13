# Authentication

## Receiving an access token

You use access tokens to authorize any requests made towards our API. To request an access token,
POST to https://callbackstaffing.com/api/oauth/access_token using either client credentials or
a username/password pair.

### Examples

#### Client Credentials

```
POST https://callbackstaffing.com/api/oauth/access_token?client_id=YOUR_CLIENT_ID&client_secret=YOUR_SECRET_KEY
```

## Grant Types

We use two different grant types when accessing our API:

### 1. The Password Grant

Using this grant type, you can authenticate one user, and then access all data belonging to this
user only. Use this grant type if you want users to take action or view their own data in your
third party application, or your website.

### 2. Client Credentials

Use this grant type if you want to fetch data on a company level, for example the schedule for one 
week.