# small
Crypto, Easy

## Description

> When things are small you have to be carefull!

```
message = int('REDACTED', base=35)
N = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]8995901030116001751822218657
c = message^3 % N
# c = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]3130656292878088405243095416128

The message is the flag. No flag format.
```

## Solution

This looks like RSA with a small exponent. This is similar to the picoCTF [miniRSA](/2019_picoCTF/miniRSA.md) challenge. 

We find the cube root of `c` and decode it as base35 (since the message itself was encoded in base35).

```python
import gmpy2

n = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]8995901030116001751822218657
e = 3
cipher_str = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]3130656292878088405243095416128 


gs = gmpy2.mpz(cipher_str)
gm = gmpy2.mpz(n)
ge = gmpy2.mpz(e)

root, exact = gmpy2.iroot(gs, ge)
print root

def base35encode(integer):
    """
    Convert from Base10 to Base35.
    Based on https://en.wikipedia.org/wiki/Base36
    """
    chars = '0123456789abcdefghijklmnopqrstuvwxy'

    sign = '-' if integer < 0 else ''
    integer = abs(integer)
    result = ''

    while integer > 0:
        integer, remainder = divmod(integer, 35)
        result = chars[remainder] + result

    return sign + result

print base35encode(root)
```

Output:
```console
root@kali:/media/sf_CTFs/36c3/small# python 1.py
6484877229948717415163579969767084212
juniorissmallkuchenblech
```