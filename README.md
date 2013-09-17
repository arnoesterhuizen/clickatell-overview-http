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
    $user     = "user";
    $password = "password";
    $api_id   = "xxxxxxx";
    $baseurl  = "http://api.clickatell.com";

    $text = urlencode("This is an example message");
    $to = "00123456789";

    //authenticate and get token
    $ret = file($baseurl/http/auth?user=$user&password=$password&api_id=$api_id);

    // explode our response. return string is on first line of the data returned
    $sess = explode(":", $ret[0]);
    if ($sess[0] == "OK") {
         $sess_id = trim($sess[1]); // remove any whitespace
        $url = "$baseurl/http/sendmsg?session_id=$sess_id&to=$to&text=$text";
 
        // do sendmsg call
        $ret = file($url);
        $send = explode(":",$ret[0]);
 
        if ($send[0] == "ID") {
            echo "successnmessage ID: ". $send[1];
        } else {
            echo "send message failed";
        }
    } else {
        echo "Authentication failure: ". $ret[0];
    }
?>
```

##Bash

##Java

##Android

##Perl

##Python

##Scala

##Node.js

##Javascript
