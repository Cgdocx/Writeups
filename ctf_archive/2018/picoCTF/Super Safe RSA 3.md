# Super Safe RSA 3
Cryptography, 600 points

## Description:
> The more primes, the safer.. right.?.?



## Solution:

Connecting to the remote service provided an output similar to the following:
```console
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA_3# nc 2018shell3.picoctf.com 54915
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]125238046027120302492188
n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]7913138960919397850924197
e: 65537
```

It turns out the RSA isn't limited to two primes - n can be created from multiple primes. Everything else still applies, so if we can factor n, we can decipher c.

A factorization calculator can be found [here](https://www.alpertron.com.ar/ECM.HTM).

The code:
```
from pwn import *
import gmpy2

c = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]0236614277782710397061777
e = 65537
n = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]9200598968770425254478111

#https://www.alpertron.com.ar/ECM.HTM
r = [2258669983,2426036101,2435230003,2457368657,2492126459,2504786551,2580640079,2633613641,2668125919,2689910999,2819565949,2846315341,2903152657,2962793963,3098061409,3456744829,3507271289,3514264627,3539275907,3577746091,3584129251,3586647227,3616554797,3625117157,3708264397,3742944367,3848405887,3865852561,3920390431,4037867321,4251606701,4269328807]

assert(n == reduce(lambda x, y: x * y, r))

phi_n = 1
for i in range(len(r)):
    phi_n *= (r[i] - 1)
log.info("phi_n: {}".format(phi_n))

d = gmpy2.invert(e, phi_n)
log.info("d: {}".format(d))

plaintext = pow(c, d, n)
log.info("plaintext: {}".format(str(plaintext)))

plaintext_decoded = (format(plaintext, 'x')).decode("hex")

log.success("Flag: {}".format(plaintext_decoded))
```

The output:
```console
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA_3# python solve.py
[*] phi_n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]5036425216000000000000000
[*] d: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]254789611008132810473473
[*] plaintext: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]
[+] Flag: picoCTF{p_&_q_n0_r_$_t!!_3543941}
```

The flag: picoCTF{p_&_q_n0_r_$_t!!_3543941}