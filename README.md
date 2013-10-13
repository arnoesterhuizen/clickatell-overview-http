# Clickatell HTTP API Overview

The full Clickatell HTTP API documentation can be found at http://www.clickatell.com/downloads/http/Clickatell_HTTP.pdf.

You can access the API endpoint on `api.clickatell.com/http` using either `http` or `https` protocols, and use either `GET` or `POST` methods. Samples in this document use `GET` for easier reading, but I recommend using `POST` in production environments.

Remember to URL encode your parameter values.

# Overview

The call is in the format `https://api.clickatell.com/http/<command>?<parameters>`

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

Below is an alpahbetical listing of the more commonly used commands with a description. To see the full list of commands, refer to [Clickatell's HTTP API documentation](http://www.clickatell.com/downloads/http/Clickatell_HTTP.pdf).

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

Prevent a message that hasn't left our queues from being sent using the [`apimsgid`](#apimsgid) parameter (returned from the [`sendmsg`](#sendmsg) command). Because we process messages as they come in, this command is only really useful for messages with a [deferred delivery time](#deliv_time) or a [scheduled delivery time in the future](#scheduled_time).

* Required parameters:
    * [`session_id`](#session_id)
    * [`apimsgid`](#apimsgid)
* Success response:
    * `ID: <apimsgid> Status: <status code>`
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [error codes and descriptions](#error-codes).

## `getbalance`

This will return the number of credits available to the account (as a floating point value).

* Required parameters:
    * [`session_id`](#session_id)
* Success response:
    * `Credit: 0.0`
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [error codes and descriptions](#error-codes).

## `getmsgcharge`

Similar to [`querymsg`](#querymsg), this is a _fallback_ method to query how many credits were used to send a message using the [`apimsgid`](#apimsgid) parameter (returned from the [`sendmsg`](#sendmsg) command). This method also returns the message status.

The preferred way to get this information is to use the [callback URL](#callback-url) parameter.

* Required parameters:
    * [`session_id`](#session_id)
    * [`apimsgid`](#apimsgid)
* Success response:
    * `ID: <apimsgid> Charge: <0.0> Status: <status code>`
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [status codes](#status-codes) and [error codes and descriptions](#error-codes).

## `querymsg`

This is a _fallback_ method to query the status of a message using the [`apimsgid`](#apimsgid) parameter (returned from the [`sendmsg`](#sendmsg) command).

The preferred way to get this information is to use the [callback URL](#callback-url) parameter.

* Required parameters:
    * [`session_id`](#session_id)
    * [`apimsgid`](#apimsgid)
* Success response:
    * `ID: <apimsgid> Status: <status code>`
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [status codes](#status-codes) and [error codes and descriptions](#error-codes).

## `ping`

Ping the service to extend your [`session_id`](#session_id) lifetime with another 15 minutes.

* Required parameters:
    * [`session_id`](#session_id)
* Success response:
    * `OK: `
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [error codes and descriptions](#error-codes).

## `sendmsg`

Send a message to a [recipient](#to):

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

#Parameters
These (and more) parameters are documented in more detail in [Clickatell's HTTP API documentation](http://www.clickatell.com/downloads/http/Clickatell_HTTP.pdf).

## `api_id`

You can sign up for multiple APIs in your Clickatell Developers' Central account, and you specify which one you would like to use using the `api_id`.

## `apimsgid`

A unique ID we return that can be used to refer to a message submitted to our servers. The `apimsgid` is a 32 character hexadecimal string.

## `callback`

Instead of polling our servers for the message status, you can specify a callback URL that we can use to notify you of status updates to the message. We will pass [`apimsgid`](#apimsgid), [`charge`](#charge), [`climsgid`](#climsgid), [`from`](#from), [`status`](#status), [`timestamp`](#timestamp) and [`to`](#to). If we're unable to access the URL, our servers will retry up to 8 times.

## `charge`

The amount of credits was used to deliver the message.

## `climsgid`

An optional unique ID you can use when sending a message. We will then return in addition to the [`apimsgid`](#apimsgid). Use alphanumeric characters of maximum of 32 characters in length.

## `concat`

Messages that are longer than 160 charaters (or 140 bytes) can be sent in multiple parts and concatenated by the receiving handset. This parameter allows you to specify the maximum number of parts to split the message into (up to a maximum of 3).

## `deliv_time`

Specify the number of minutes to delay the message delivery. The minimum value for this is 5 minutes, and the maximum is 10080 minutes (or 7 days).

## `from`

Specify a source address (also known as a sender ID) to display on the recipient's handset. This is mostly used when you need recipients to be able to reply to your message, and must first be configured in your Clickatell Developers' Central account. Setting the `from` parameter isn't guaranteed to work with all mobile networks or handsets. Refer to the [`mo`](#mo) parameter for more information.

## `mo`

To make sure that the `from` parameter works as expected, set this parameter to 1 and we will only use mobile networks that allow a source address to be specified. This is only applicable if you you need recipients to be able to reply to your messages, and might mean we have to use more expensive delivery methods.

## `password`

This is the password you used when signing up for your Clickatell Developers' Central account.

## `req_feat`



## `scheduled_time`
## `session_id`
## `status`
## `text`
## `timestamp`
## `to`
## `token`

## `user`

This is the username you used when signing up for your Clickatell Developers' Central account.
