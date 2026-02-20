---
title: Rate Limits
full_title: Rate Limits of the DeepInfra endpoints | ML Models | DeepInfra
description: Find information about Rate Limits of DeepInfra endpoints, and more!
---

## 200 concurrent requests

By default every account has 200 concurrent requests limit per model. If you are querying two different models simultaneously 
you will be able to handle a total of 400 concurrent requests, 200 for each.

We've observed that this is plenty even for applications and serivces with hundreds of thousands of daily active users.

For large processing batch jobs, like doing embeddings on a knowledge base, you can use something like
[token bucket rate limiting algorithm](https://en.wikipedia.org/wiki/Token_bucket) to keep under 200 concurrent requests. You will
still be able to finish you work in a reasonable amount of time.

If you need more just let us know why and depending on your case we might raise it.
You can request rate limit increase in your [dashboard](/dash/account)

## Understanding Concurrent Requests

A concurrent requests limit is the maximum number of requests processed simultaneously. To illustrate how concurrent requests work, let's consider an example:

Imagine your application is making requests to our system and has reached the 200 concurrent request limit. Suddenly, 10 of those requests are completed, freeing up 10 slots. Your application can now send 10 new requests to our system, which will then be processed concurrently with the remaining 190 requests. This means that even though you've reached the concurrent request limit, your application can still continue to send new requests as old ones are completed.


The rate at which you can send new requests depends on how long each request takes to process. The actual number of requests per minute (RPM) varies based on the duration of each request. Here are some examples:

| Avg Request Duration | Limit | Approximate RPM                                                          |
|:---------------------|:-----:|:-------------------------------------------------------------------------|
| 1 second             |  200  | 12000 RPM (200 concurrent requests x 60 seconds / 1 second per request)  |
| 10 seconds           |  200  | 1200 RPM (200 concurrent requests x 60 seconds / 10 seconds per request) |
| 60 seconds           |  200  | 200 RPM (200 concurrent requests x 60 seconds / 60 seconds per request)  |


## Purpose of rate limits

Rate limits are established protocols designed to prevent abuse or misuse of the API. They ensure fair and consistent access to the API for all users while maintaining reliable performance.


## How do you check for rate limits?

You will be getting the HTTP **429** response status code with **Rate limited** message. 

Actions to take:
* retry in a bit
* or slow down your requests
* or apply for increase by contacting us

Note: sometimes you might get **429** errors when the model gets too busy. Typically, the auto-scaling logic will kick in. So if you retry in just a bit, it should get resolved.