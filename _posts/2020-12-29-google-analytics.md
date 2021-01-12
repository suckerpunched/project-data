---
layout: post
title:  "Google Analytics"
# date:   2020-12-29 19:11:18 -0800
categories: python3 google-analytics
---

In this example, we will be getting you set up to pull analytics data from Google Analytics API. This example will assume you've already created a service account and have downloaded the credentials json file.

---
<br/>

#### Import Packages

We'll start by importing `service_account` from `google.oauth2` and `build` from `apiclient.discovery`, both of these packages will be required to interact with api via the sdk.
```python
from google.oauth2 import service_account
from apiclient.discovery import build
```
<br/>

#### Build Credentials
Next we'll setup our credentials. First, we will need to import our clients secret file (credentials json file from google). After which, we'll need to specify our desired scopes. Finally, we will need to pass all this information to `service_account` using the appropriate function.

```python
client_secrets = {
    'type': ... ,
    'project_id': ... ,
}
```
```python
scopes = ['https://www.googleapis.com/auth/analytics.readonly']
credentials = service_account.Credentials.from_service_account_info(
    client_secret, 
    scopes=scopes,
)
```
<br/>

#### Build Service Request Handler

Now we'll pass the service name, the version, and our credentials to `build`, which will then allow us to interact and retreive data.
```python
service = build('analytics', 'v3', credentials=credentials)
reporting = build('analyticsreporting', 'v3', credentials=credentials)
```
<br/>

#### Get Linked Accounts

In order to generate reports, we will need to specify a __view id__. Instead of trying to hunt down a particular id. Here we will generate an object that contains all the accounts we have access to.
```python
my_accounts = {}
for account in service.management().accountSummaries().list().execute()['items']:
    for summary in account['webProperties']:
        for profile in summary['profiles']:

            my_accounts.setdefault(profile['id'], [])
            my_accounts[profile['id']].append([ 
                account['id'], 
                summary['id'], 
                summary['websiteUrl'],
            ])
```
<br/>
