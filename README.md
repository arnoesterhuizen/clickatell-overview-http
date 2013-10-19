# Clickatell HTTP API Overview

The full Clickatell HTTP API documentation can be found at https://www.clickatell.com/downloads/http/Clickatell_HTTP.pdf, and the two-way documentation can be found at https://www.clickatell.com/downloads/Clickatell_two-way_technical_guide.pdf.

You can access the API endpoint on `api.clickatell.com/http` using either `http` or `https` protocols, and use either `GET` or `POST` methods. Samples in this document use `GET` for easier reading, but Clickatell recommends using `POST` in production environments.

Remember to URL encode your parameter values.

# Overview: Sending Messages

All API calls are in the format `https://api.clickatell.com/http/<command>?<parameters>`

See a list of [commands](#commonly-used-commands) and [parameters](#parameters).

# Commonly Used Commands

The most frequently used commands are:
* [`auth`](#auth) &ndash; Authenticate before you start sending messages
* [`sendmsg`](#sendmsg) &ndash; Send a message
* [`token_pay`](#token_pay) &ndash; Redeem a voucher for credits

Other commonly used commands include:
* [`querymsg`](#querymsg) &ndash; Query the status of a message
* [`getmsgcharge`](#getmsgcharge) &ndash; Get the charge and status of a message that was sent
* [`ping`](#ping) &ndash; Prevent a session from expiring by extending it another 15 minutes
* [`delmsg`](#delmsg) &ndash; Delete a message if it hasn't left our system
* [`getbalance`](#getbalance) &ndash; Get the balance of the specified account

Below is an alpahbetical listing of the more commonly used commands with a description. To see the full list of commands, refer to [Clickatell's HTTP API documentation](https://www.clickatell.com/downloads/http/Clickatell_HTTP.pdf).

## `auth`

Authenticate and get a [`session_id`](#session_id) that expires after 15 minutes of inactivity.

* Required parameters:
    * [`api_id`](#api_id)
    * [`user`](#user)
    * [`password`](#password)
* Success response:
    * `OK: <session_id>`
* Failure response:
    * `ERR: <error code>, <error description>`

The [`session_id`](#session_id) is a 32 character hexadecimal string. See a list of [error codes and descriptions](#error-codes).

## `delmsg`

Prevent a message that hasn't left Clickatell's message queues from being sent. Because Clicaktell processes messages immediately, this command is only useful for messages with a [deferred delivery time](#deliv_time) or a [scheduled delivery time in the future](#scheduled_time).

* Required parameters:
    * [`session_id`](#session_id)
    * [`apimsgid`](#apimsgid)
* Success response:
    * `ID: <apimsgid> Status: <status code>`
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [status codes](#status) and [error codes and descriptions](#error-codes).

## `getbalance`

This will return the number of credits available to the account (as a floating point value).

* Required parameters:
    * [`session_id`](#session_id)
* Success response:
    * `Credit: <0.0>`
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [error codes and descriptions](#error-codes).

## `getmsgcharge`

This is a fallback method to query how many credits were used to send a message. The preferred way to get this information is to use the [`callback`](#callback) parameter. This method also returns the message status.

* Required parameters:
    * [`session_id`](#session_id)
    * [`apimsgid`](#apimsgid)
* Success response:
    * `ID: <apimsgid> Charge: <0.0> Status: <status code>`
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [status codes](#status) and [error codes and descriptions](#error-codes). See also [`querymsg`](#querymsg).

## `querymsg`

This is a fallback method to query the status of a message. The preferred way to get this information is to use the [`callback`](#callback) parameter.

* Required parameters:
    * [`session_id`](#session_id)
    * [`apimsgid`](#apimsgid)
* Success response:
    * `ID: <apimsgid> Status: <status code>`
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [status codes](#status) and [error codes and descriptions](#error-codes). See also [`getmsgcharge`](#getmsgcharge).

## `ping`

Ping the service to extend your session lifetime with another 15 minutes.

* Required parameters:
    * [`session_id`](#session_id)
* Success response:
    * `OK: `
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [error codes and descriptions](#error-codes).

## `sendmsg`

Send a message to a recipient.

* Required parameters:
    * [`session_id`](#session_id)
    * [`to`](#to)
    * [`text`](#text)
* Success response:
    * `ID: <apimsgid>`
* Failure response:
    * `ERR: <error code>, <error description>`

You can also send a message to multiple recipients by passing a comma-seperated list of destination numbers.

* Success response; multiple recipients:
    * `ID: <apimsgid> To: <recipient>`
* Failure response; multiple recipients:
    * `ERR: <error code>, <error description> To: <recipient>`
* Mixed response; multiple recipients:
    * `ID: <apimsgid> To: <recipient>`
    * `ERR: <error code>, <error description> To: <recipient>`

See a list of [error codes and descriptions](#error-codes).

## `token_pay`

Use this command to redeem a voucher for message credits.

* Required parameters:
    * [`session_id`](#session_id)
    * [`token`](#token)
* Success response:
    * `OK: 605`
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [error codes and descriptions](#error-codes).

# Parameters
These (and more) parameters are documented in more detail in [Clickatell's HTTP API documentation](https://www.clickatell.com/downloads/http/Clickatell_HTTP.pdf).

## `api_id`

You can sign up for multiple APIs in your Clickatell Developers' Central account, and you specify which one you would like to use using the `api_id`.

## `apimsgid`

A unique ID we return that can be used to refer to a message submitted to our servers. The `apimsgid` is a 32 character hexadecimal string.

## `callback`

Specify which message statuses to inform you of. You need to set a callback URL in the API settings in Clickatell Developers' Central.

We will pass [`apimsgid`](#apimsgid), [`charge`](#charge), [`climsgid`](#climsgid), [`from`](#from), [`status`](#status), [`timestamp`](#timestamp) and [`to`](#to). If we're unable to access the URL, our servers will retry up to 8 times.

Possible values for the `callback` parameter are:

| Callback Value  | Status Codes that will trigger a callback (See [`status`](#status)) | Detail |
|-----------------|---------------------------------------------------------------------|---|
|0                |                                  |No callback triggered                 |
|1                |003                               |Intermediate statuses                 |
|2                |004, 005, 007, 009, 010, 012      |Final statuses                        |
|3                |003, 004, 005, 007, 009, 010, 012 |Intermediate and final statuses       |
|4                |005, 007, 009, 010, 012           |Error statuses                        |
|5                |003, 005, 007, 009, 010, 012      |Intermediate and error statuses       |
|6                |004, 005, 007, 009, 010, 012      |Final and error statuses              |
|7                |003, 004, 005, 007, 009, 010, 012 |Intermediate, final and error statuses|

Currently all error statuses are also final statuses, but in the future we may return error statuses that are not final.

The retry cycle for status updates is:

1. 2 minutes after first attempt
1. 4 minutes after previous attempt
1. 8 minutes after previous attempt
1. 16 minutes after previous attempt
1. 32 minutes after previous attempt
1. 64 minutes after previous attempt
1. 128 minutes after previous attempt
1. 3 days after previous attempt

After the last attempt we will not retry again, but you can still log in to Clickatell Developers' Central and get the message's latest status via our reporting tools.

## `charge`

The amount of credits that was used to deliver the message.

## `climsgid`

An optional unique ID you can use when sending a message. We will then return in addition to the [`apimsgid`](#apimsgid). Use alphanumeric characters of maximum of 32 characters in length.

## `concat`

Messages that are longer than 160 charaters (or 140 bytes) can be sent in multiple parts and concatenated by the receiving handset. This parameter allows you to specify the maximum number of parts to split the message into (up to a maximum of 3).

## `deliv_time`

The number of minutes to delay the message delivery. The maximum is 10080 minutes (or 7 days). Please note that the message can be sent out up to 5 minutes later than specified.

## `from`

The source address (also known as a sender ID) to display on the recipient's handset. This is mostly used when you need recipients to be able to reply to your message, and must first be configured in your Clickatell Developers' Central account. Setting the `from` parameter isn't guaranteed to work with all mobile networks or handsets. Refer to the [`mo`](#mo) parameter for more information.

Similar to the [`to`](#to) parameter, this needs to be in standard international format (e.g. including the coutry code, and using only digits &ndash; without any spaces or "+").

## `mo`

To make sure that the [`from`](#from) parameter works as expected, set this parameter to 1 and we will only use mobile networks that allow a source address to be specified. This is only applicable if you you need recipients to be able to reply to your messages, and might mean we have to use more expensive delivery methods.

## `password`

This is the password you used when signing up for your Clickatell Developers' Central account.

## `scheduled_time`

The unix timestamp of when we should deliver the message. You can schedule up to 7 days in advance. Please note that the message can be sent out up to 5 minutes later than specified.

## `session_id`

The unique session ID we send after you authenticate. This is valid for 15 minutes after the last API call, but you can keep the session alive by using the [`ping`](#ping) command.

## `status`

As messages get processed, we update the message status. Message statuses are:

Status Code|Status Description|Comment
----|----|----
001|Message unknown|The message ID is incorrect or reporting is delayed.
002|Message queued|The message could not be delivered and has been queued for attempted redelivery.
003|Delivered to gateway|Delivered to the upstream gateway or network (delivered to the recipient). 
004|Received by recipient|Confirmation of receipt on the handset of the recipient.
005|Error with message|There was an error with the message, probably caused by the content of the message itself.
006|User cancelled message delivery|The message was terminated by a user (stop message command) or by our staff.
007|Error delivering message|An error occurred delivering the message to the handset.
008|OK|Message received by gateway. 
009|Routing error|An error occurred while attempting to route the message.
010|Message expired|Message has expired before we were able to deliver it to the upstream gateway. No charge applies.
011|Message queued for later delivery|Message has been queued at the gateway for delivery at a later time (delayed delivery).
012|Out of credit|The message cannot be delivered due to a lack of funds in your account. Please re-purchase credits.
014|Maximum MT limit exceeded|The allowable amount for MT messaging has been exceeded. 

## `text`

The body of the message you would like to send. If the text is more than 160 characters, use the [`concat`](#concat) parameter to make them concatenate on supported handsets. 

For messages with special characters you will need to encode it using http://rishida.net/tools/conversion/, and set the [`unicode`](#unicode) parameter to 1.

## `timestamp`

The unix timestamp we return with every message status update to indicate when the status change was triggered.

## `to`

The recipient address in standard international format (e.g. including the coutry code, and using only digits &ndash; without any spaces or "+")

## `token`

The credit voucher token you would like to redeem. this will add credits to your account.

## `unicode`

Specify this parameter with a value of 1 if you have special characters in your message. See the description of the [`text`](#text) parameter.

## `user`

This is the username you used when signing up for your Clickatell Developers' Central account.

# Overview: Receiving Messages

Callback URLs will be used to hand received messages to your application. You need to configure this in Clickatell's Developers' Central under the "Receiving Messages" menu. 

We will pass [`api_id`](#api_id), [`momsgid`](#momsgid), [`from`](#from), [`timestamp`](#timestamp), [`text`](#text) and [`to`](#to). If applicable, we will also pass [`charset`](#charset) and [`udh`](#udh) parameters. If we're unable to access the URL, our servers will retry up to 5 times.

The retry cycle for incoming message callbacks is:
1. 2 minutes after first attempt
1. 4 minutes after previous attempt
1. 8 minutes after previous attempt
1. 16 minutes after previous attempt
1. 25 minutes after previous attempt

After the last attempt we will not retry again, but you can still log in to Clickatell Developers' Central and get the message details via our reporting tools.

# Error Codes

Error Code|Error Description|Comment
---|---|---
001|Authentication failed|Authentication details are incorrect.
002|Unknown username or password|Authorization error, unknown user name or incorrect password.
003|Session ID expired|The session ID has expired after a pre-set time of inactivity.
005|Missing session ID|Missing session ID attribute in request.
007|IP Lockdown violation|You have locked down the API instance to a specific IP address and then sent from an IP address different to the one you set.
101|Invalid or missing parameters|One or more required parameters are missing or invalid
102|Invalid user data header|The format of the user data header is incorrect.
103|Unknown API message ID|The API message ID is unknown. Log in to your API account to check the ID or create a new one.
104|Unknown client message ID|The client ID message that you are querying does not exist.
105|Invalid destination address|The destination address you are attempting to send to is invalid.
106|Invalid source address|The sender address that is specified is incorrect.
107|Empty message|The message has no content
108|Invalid or missing API ID|The API message ID is either incorrect or has not been included in the API call.
109|Missing message ID|This can be either a client message ID or API message ID. For example when using the stop message command.
113|Maximum message parts exceeded|The text message component of the message is greater than the permitted 160 characters (70 Unicode characters). Select concat equal to 1,2,3-N to overcome this by splitting the message across multiple messages.
114|Cannot route message |This implies that the gateway is not currently routing messages to this network prefix. Please email support@clickatell.com with the mobile number in question. 
115|Message expired|Message has expired before we were able to deliver it to the upstream gateway. No charge applies
116|Invalid Unicode data|The format of the unicode data entered is incorrect.
120|Invalid delivery time|The format of the delivery time entered is incorrect.
121|Destination mobile number blocked|This number is not allowed to receive messages from us and has been put on our block list.
122|Destination mobile opted out|The user has opted out and is no longer subscribed to your service.
123|Invalid Sender ID|A sender ID needs to be registered and approved before it can be successfully used in message sending.
128|Number delisted|This error may be returned when a number has been delisted.
130|Maximum MT limit exceeded until <UNIX TIME STAMP>|This error is returned when an account has exceeded the maximum number of MT messages which can be sent daily or monthly. You can send messages again on the date indicated by the UNIX TIMESTAMP.
201|Invalid batch ID|The batch ID which you have entered for batch messaging is not valid. 
202|No batch template|The batch template has not been defined for the batch command.
301|No credit left|Insufficient credits
901|Internal error|Please retry
