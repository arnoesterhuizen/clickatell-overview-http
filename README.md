# Clickatell HTTP API Overview

You can access the API endpoint on `api.clickatell.com/http` using either `http` or `https` protocols, and use either `GET` or `POST` methods. Samples in this document use `GET` for easier reading, but I recommend using `POST` in production environments.

Remember to URL encode your parameter values.

# Call Format

The call is in the format `https://api.clickatell.com/http/<command>?<parameters>`

See a list of [commands](#commands) and [parameters](#parameters).

# Commands

The most frequently used commands are:
* [`auth`](#auth) &ndash; Authenticate and receive a session ID
* [`sendmsg`](#) &ndash; Send a message

Other useful commands include:
* [`ping`](#) &ndash; Prevent a session from expiring by extending it another 15 minutes
* [`querymsg`](#) &ndash; Query the status of a message
* [`delmsg`](#) &ndash; Delete a message if it hasn't left our system
* [`getbalance`](#) &ndash; Get the balance of the specified account
* [`getmsgcharge`](#) &ndash; Get the charge and status of a message that was sent

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

The session ID is a 32 character hexadecimal string. See a list of [error codes and descriptions](#error-codes).

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

## `ping`

Ping the service to extend your [`session_id`](#session_id) lifetime with another 15 minutes.

* Required parameters:
    * [`session_id`](#session_id)
* Success response:
    * `OK: `
* Failure response:
    * `ERR: <error code>, <error description>`

See a list of [error codes and descriptions](#error-codes).

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

## `delmsg`

Prevent a message that hasn't left our queues from being sent using the [`apimsgid`](#apimsgid) parameter (returned from the [`sendmsg`](#sendmsg) command). Because we process messages as they come in, this command is only really useful for messages with a [deferred delivery time](#deliv_time).

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

#Parameters

## `api_id`

#Responses

