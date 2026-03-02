# triple-secure
Crypto, 150 points

## Description

> To get the flag, you must break RSA not once, but three times!

Two files were attached:

`encrypt.py`:

```python
#!/usr/bin/env python3

from Crypto.Util.number import getPrime, bytes_to_long

with open('flag.txt', 'rb') as f:
    flag = f.read()

p = getPrime(1024)
q = getPrime(1024)
r = getPrime(1024)

n1 = p * q
n2 = p * r
n3 = q * r

moduli = [n1, n2, n3]

e = 65537
c = bytes_to_long(flag)

for n in moduli:
    c = pow(c, e, n)

with open('public-key.txt', 'w') as f:
    f.write(f'n1: {n1}\n')
    f.write(f'n2: {n2}\n')
    f.write(f'n3: {n3}\n')
    f.write(f'e: {e}\n')
    f.write(f'c: {c}\n')
```

`public-key.txt`:

```
n1: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]50871764431562609
n2: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]68224103778645691
n3: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]23188480059886131
e: 65537
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]8691228620744490

```

## Solution

We can see from the script implementation that the flag is RSA-encrypted three times with three different modulus keys. However, the keys are created by just three random prime numbers, and every modulus value shares a prime with another modulus value. Therefore, it's trivial to factor the modulus to prime factors using the GCD of two modulus values. Once we have the prime factors of each modulus, it's trivial to decrypt RSA by the book - we just need to recursively apply RSA decryption for three times.

The full script:

```python
from gmpy2 import *

def decrypt(c, p, q, e):
     ph = (p-1)*(q-1)
     d = invert(e, ph)
     return pow(c, d, p*q)

params = {}
with open("public-key.txt") as f:
    for line in f:
        line = line.rstrip()
        name, value = line.split(":")
        params[name] = mpz(int(value))

p = gcd(params["n1"], params["n2"])
q = gcd(params["n1"], params["n3"])
r = gcd(params["n2"], params["n3"])

assert(p * q == params["n1"])
assert(p * r == params["n2"])
assert(q * r == params["n3"])

a1 = decrypt(params["c"], q, r, params["e"])
a2 = decrypt(a1, p, r, params["e"])
a3 = decrypt(a2, p, q, params["e"])

print(bytes.fromhex(format(a3, 'x')).decode("ascii"))
```

Output:

```console
┌──(user@kali)-[/media/sf_CTFs/pico/triple-secure]
└─$ python3 solve.py
picoCTF{1_gu3ss_tr1pl3_rs4_1snt_tr1pl3_s3cur3!!!!!!}
```