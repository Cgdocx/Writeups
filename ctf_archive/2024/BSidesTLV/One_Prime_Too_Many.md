# One Prime Too Many

 * Category: Cryptography
 * 150 Points
 * Solved by the JCTF Team

## Description

> I know how to use RSA, don't you tell me how to use it correctly!

`enc.py`: 

```python
from secret import FLAG
from Crypto.Util.number import getPrime, bytes_to_long, long_to_bytes
import binascii


def gen_key(bits):
    N = getPrime(bits)
    e = 65537
    return N, e


def encrypt_message(message, N, e):
    m_int = bytes_to_long(message.encode('utf-8'))
    ciphertext = pow(m_int, e, N)
    return binascii.hexlify(long_to_bytes(ciphertext))


N, e = gen_key(2048)
ciphertext = encrypt_message(FLAG, N, e)

with open('output.txt', 'w') as f:
    f.write(f"{N}\n")
    f.write(ciphertext.decode())

```

`output.txt`:

```
[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]66151746149108457
[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]a327e904972a308600d73b6e06dcc9e3
```

## Solution

The code in `enc.py` looks like RSA, with one significant difference. While in standard RSA,
$N$ is the product of two prime numbers, in our case, it's a prime number itself.  
This has very dramatic consequences: It transforms the calculation of $\varphi(N)$ from 
extrimely hard to trivial. In fact, if $N$ is prime, then $\varphi(N) = N - 1$.

Once we have $\varphi(N)$, we can use it to calculate $d$ using the standard formula: $d = e^{-1} (mod(\varphi(N)))$. Then, we can proceed to decrypt the message as usual: $m = c^d (mod (N))$.

Script:

```python
import gmpy2
from Crypto.Util.number import long_to_bytes

with open("output.txt") as f:
    n = int(f.readline())
    c = int(f.readline(), 16)
    e = 65537

    phi_n = n - 1
    d = gmpy2.invert(e, phi_n)
    m = pow(c, d, n)
    print(long_to_bytes(m).decode())
```

Output: 

```console
┌──(user@kali)-[/media/sf_CTFs/bsides/One_Prime_Too_Many]
└─$ python3 solve.py
BSidesTLV2024{wh0_g4v3_u_pr1m3_N?}
```