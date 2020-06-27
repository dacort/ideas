# Twitter Stats in Slack

Get basic graphs for Twitter search results in Slack.

```
/tweetstats #realtalk
/tweetstats #realtalk for Aug 2018
```

## Requirements

- Twitter [premium APIs](https://developer.twitter.com/en/premium-apis.html): $99/month for 100 requests
- Slack integration - admin needs to add + webhooks to handle
- Graph generator - I used Highcharts for this previously
- Backend query parser and API request maker

## Architecture

OK, so I'm thinking maybe I do like a Docker-based architecture?
(But this is wise advice: https://twitter.com/kamal/status/1091020666206179328)

1) Need an API server to do the following:
    1.1) Parse user queries into (keyword,start,end) tuples
        1.1.1) May want to allow users specify breakdown (minute/hour/day)
        1.1.2) Need a good lib (or to modify when) to parse dates
    1.2) Execute a search against the Twitter Search API
    1.3) Update/maintain user quota information
    1.4) Generate awesome graphs on-demand
        1.4.1) highcharts npm server?
    1.5) Receive webhook requests from Slack, possibly fork off for delayed responses
    1.6) Generate a CSV file of the stats

2) Need a data store to track the following:
    2.1) Accounts
    2.2) Secret Info
        2.2.1) Slack credentials
        2.2.2) Twitter credentials
    2.3) Monthly quota per Account
    2.4) Individual requests and status
    2.5) Cached stats response (0_o)

3) Need a UI for the following:
    3.1) User signup
    3.2) Slack authorization flow
    3.3) Twitter authorization flow
    3.4) Billing

4) Do I need any sort of background processing engine?
    4.1) Maybe? Could use the birdfeeder approach. 
    4.2) Write request/Slack token into DB, process, then send back.

## Billing

30-day archive: 500 requests/$150
Full archive: 100 requests/$99

By default, we maintain Twitter credentials and endpoint. Eventually, we'll allow users to specify their own.

Two ways I think to charge users up front:
1) Per request
2) Monthly batch

We need to maintain a record of each account's <period> maximum and <period> usage.