# Mind your Ps and Qs
Category: Crypto, 20 points

## Description

> In RSA, a small e value can be problematic, but what about N? Can you decrypt this?

```
Decrypt my super sick RSA:
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]6
n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]7
e: 65537
```

## Solution

`n` is small and can be factored into `p` and `q`:

```python
from factordb.factordb import FactorDB
import gmpy2

c = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]6
n = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]7
e = 65537

f = FactorDB(n)
f.connect()
p, q = f.get_factor_list()
ph = (p-1)*(q-1)
d = gmpy2.invert(e, ph)
plaintext = pow(c, d, n)
print("Flag: {}".format(bytearray.fromhex(format(plaintext, 'x')).decode()))
```

Output:
```console
┌──(user@kali)-[/media/sf_CTFs/pico/Mind_your_Ps_and_Qs]
└─$ python3 solve.py
Flag: picoCTF{sma11_N_n0_g0od_23540368}
```

The flag: `picoCTF{sma11_N_n0_g0od_23540368}`