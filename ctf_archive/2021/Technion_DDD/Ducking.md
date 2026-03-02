#  Ducking
Category: Steganography, 50 points

## Description

> I hid a flag in my favorite photo, try to find it! 🦆🦆🦆 

An image was attached.

## Solution

We start by searching for strings within the attached image:

```console
┌──(user@kali)-[/media/sf_CTFs/ducky_debug_duck/Ducking]
└─$ strings ducking.jpg -n 10
passphrase:duck
'9=82<.342
![AWS_SECRET_REMOVED]2222222222
%&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
&'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
xVW-lDQXas
dU*1Uv.ThCt
```

Notice "passphrase:duck" - that's a clear hint for using `steghide`:

```console
┌──(user@kali)-[/media/sf_CTFs/ducky_debug_duck/Ducking]
└─$ steghide extract -sf ducking.jpg -p duck
wrote extracted data to "flag.txt".

┌──(user@kali)-[/media/sf_CTFs/ducky_debug_duck/Ducking]
└─$ cat flag.txt
flag{qu4ck_qu4ck}
```