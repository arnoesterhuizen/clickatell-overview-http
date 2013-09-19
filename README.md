# Clickatell HTTP API Overview

You can access the API endpoint on `api.clickatell.com/http` using either http or https protocols, and use either `GET` or `POST` methods.

# Call Format

The call is in the format `https://api.clickatell.com/http/<command>/<parameters>`

# Commands

* `auth` -- Start a message session and receive a session ID
* `ping` -- Prevent a session from expiring
* `sendmsg` -- Send a message
* `querymsg` -- Query the status of a message
* `delmsg` -- Delete a message if it hasn't left our system
* `getbalance` -- 
* `getmsgcharge`

##Authenticate

* Command: `auth`
* Required:
    * [`api_id`](#parameters-api-id),
    * [`user`](#parameters-user),
    * [`password`](#parameters-password)
* Response: 
    * `OK: 123451`,
    * `ERR: 203, Description`

#Parameters

#Responses

#Sample Code

C#
--

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
