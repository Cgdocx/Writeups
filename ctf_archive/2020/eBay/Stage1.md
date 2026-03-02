# Stage 1

## Description

> Decipher this to have the key to the next stage.
> 
> `[AWS_SECRET_REMOVED]bXpmYmFiZXdjZXlxeGFsdXIv`

## Solution

This is an easy warm-up question. We decode the string as base-64 and get:

```console
root@kali:/media/sf_CTFs/ebay/1# echo [AWS_SECRET_REMOVED]bXpmYmFiZXdjZXlxeGFsdXIv | base64 -d
https://rnd.ebay.co.il/riddle/mzfbabewceyqxalur/
```