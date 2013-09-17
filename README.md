# SMS API Summary
An API is a set of rules that govern how two pieces of software integrate and interact. The API documentation helps the client's software connect with Clickatell's SMS gateway in order to send SMSs without having to worry about the nitty gritty details of how it's done.

##HTTP/S
An HTTP/S API is a base level API for small businesses that is simple to use. It sends a limited amount of messages per burst (maximum 50-100) and can be integrated with a broad range of other software programs. It supports all of your core SMS gateway functionality and is your entry level connection to these functions.

#Popular Calls

##Call 1

##Call 2

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
$baseurl  = 'http://api.clickatell.com';

//message details
$to   = '00123456789';
$text = urlencode("This is an example message");

//authenticate and get token
$request  = $baseurl . '/http/auth?user=' . $user . '&password=' . $password . '&api_id=' . $api_id;
$response = file_get_contents($request);
//explode response. return string is on first line of the data returned
$session = explode(': ', $response);

if ($session[0] == 'OK') {
	//remove any whitespace
	$session_id = trim($session[1]);

	//send message
	$request  = $baseurl . '/http/sendmsg?session_id=' . $session_id . '&to=' . $to . '&text=' . $text;
	$response = file_get_contents($request);
	//explode response. return string is on first line of the data returned
	$send = explode(': ', $response);

	if ($send[0] == 'ID') {
		echo 'Message sent; Message ID: ' . $send[1];
	} else {
		echo 'Message sending failed; ' . $send[0] . ': ' . $send[1];
	}
} else {
	echo 'Authentication failure: ' . $response[0];
}
```

##Bash

##Java

##Android

##Perl

##Python

##Scala

##Node.js

##Javascript
