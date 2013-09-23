# Clickatell HTTP API Overview

You can access the API endpoint on `api.clickatell.com/http` using either http or https protocols, and use either `GET` or `POST` methods. Samples in this document use `GET` for easier reading, but I recommend using `POST` in production environments.

Remember to URL encode your parameter values.

# Call Format

The call is in the format `https://api.clickatell.com/http/<command>?<parameters>`

See a list of [commands](#commands) and [parameters](#parameters).

# Commands

* [`auth`](#auth) &ndash; Start a message session and receive a session ID
* [`ping`](#) &ndash; Prevent a session from expiring by extending it another 15 minutes
* [`sendmsg`](#) &ndash; Send a message
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

Send one or more messages using a comma seperated list of [recipients](#to)
* Required parameters:
    * [`session_id`](#session_id)
    * [`to`](#to)
    * [`text`](#text)
* Success response; single recipient:
    * `ID: <apimsgid>`
* Failure response; single recipient:
    * `ERR: <error code>, <error description>`
* Success response; multiple recipients:
    * `ID: <apimsgid> To: <recipient>`
    * `ID: <apimsgid> To: <recipient>`
    * `...`
    * `ID: <apimsgid> To: <recipient>`

See a list of [error codes and descriptions](#error-codes).

#Parameters

## `api_id`

#Responses

#Sample Code

C#
------

##.Net

##Delphi

##Clojure

##Haskell

##Ruby

##PHP Sample Code
```php
<?php
//authentication details
$user     = urlencode('user');
$password = urlencode('password');
$api_id   = '3442105';
$baseurl  = 'https://api.clickatell.com/';
$api      = 'http';

//message details
$to   = '00123456789';
$text = urlencode("This is an example message");

//authenticate and get token
$request  = $baseurl . $api . '/auth?user=' . $user . '&password=' . $password . '&api_id=' . $api_id;
$response = file_get_contents($request);
//explode response. return string is on first line of the data returned
$session = explode(': ', $response);

if ($session[0] == 'OK') {
	//remove any whitespace
	$session_id = trim($session[1]);

	//send message
	$request  = $baseurl . $api . '/sendmsg?session_id=' . $session_id . '&to=' . $to . '&text=' . $text;
	$response = file_get_contents($request);
	//explode response. return string is on first line of the data returned
	$send = explode(': ', $response);

	if ($send[0] == 'ID') {
		echo 'Message sent; Message ID: ' . $send[1];
	} else {
		echo 'Message sending failed; ' . $send[0] . ': ' . $send[1];
	}
} else {
	echo 'Authentication failure: ' . $response;
}
```

Also see https://github.com/arcturial/clickatell for more advanced examples and a library.

##Bash

##Java

##Android

##Perl

##Python

```python


```

Also see https://pypi.python.org/pypi/python-clickatell for more advanced examples and a library.

##Scala

##Node.js

##Javascript
