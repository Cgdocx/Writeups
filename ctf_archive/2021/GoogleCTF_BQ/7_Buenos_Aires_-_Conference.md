# Buenos Aires - Conference
Category: Crypto

## Description

> You are showing the invitation so that you can enter the conference. There are hundreds of important looking people at the conference. You take a glass of champagne from a tray, and try to look important yourself. After being busy with trying to look important for a few minutes, you approach the person that you are here to get classified information from. He introduces himself as Dr. Nowak Wasilewski. Nowak asks who you are, and if you can prove your knowledge through a test that he has designed by himself.
> 
> Challenge: ReadySetAction (crypto)
> 
> Apparently this script was used to encrypt super secret messages. Maybe there is something interesting in it

```python
from Crypto.Util.number import *

flag = b"REDACTED"

p = getPrime(1024)
q = getPrime(1024)
n = p*q

m = bytes_to_long(flag)

c = pow(m,3,n)

print(c)
print(n)
#[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]62181672206606709
#[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]91376093215124477
```

## Solution

This is RSA with a small exponent (3), we'll use the following script (recycled from [PicoCTF](https://github.com/Dvd848/CTFs/blob/master/2021_picoCTF/Mini_RSA.md)) to attack it:

```python
import gmpy2


n = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]91376093215124477

e = 3

c = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]62181672206606709

for i in range(10000):
    m, is_true_root = gmpy2.iroot(i*n + c, e)
    if is_true_root:
        print(f"Found i = {i}")
        print("Message: {}".format(bytearray.fromhex(format(m, 'x')).decode()))
        break
```

Output:

```console
┌──(user@kali)-[/media/sf_CTFs/google/7_Buenos_Aires_-_Conference]
└─$ python3 solve.py
Found i = 1831
Message: CTF{34sy_RS4_1s_e4sy_us3}
```