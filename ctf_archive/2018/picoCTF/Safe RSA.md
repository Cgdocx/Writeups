# Safe RSA
Crypto, 250 points

## Description:
> Now that you know about RSA can you help us decrypt this ciphertext? We don't have the decryption key but something about those values looks funky.. 
```
N: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]946599836959811
e: 3

ciphertext (c): [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]38851038892082799389023900415351164773 
```

## Solution:

Since [RsaCtfTool](https://github.com/Ganapati/RsaCtfTool) wasn't able to recover the private key, I searched for attacks against small exponents and found [this](https://l34rn-p14y.blogspot.com/2014/03/inctf-2014-crypto-200.html) and [this](https://eindbazen.net/2012/05/plaidctf-2012-rsa/).

I was able to reduce their code to the following, given the values above:
```python
import gmpy2

n = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]946599836959811
e = 3
cipher_str = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]38851038892082799389023900415351164773

gs = gmpy2.mpz(cipher_str)
gm = gmpy2.mpz(n)
ge = gmpy2.mpz(e)

root, exact = gmpy2.iroot(gs, ge)
print format(root, 'x').decode('hex')
```

Explanation: 

RSA encryption is performed by calculating C=M^e(mod n).

However, if n is much larger than e (as is the case here), and if the message is not too long (i.e. small M), then M^e(mod n) == M^e and therefore M can be found by calculating the e-th root of C.

The result:
```console
root@kali:/media/sf_CTFs/pico/Safe_RSA# python decode.py
picoCTF{e_w4y_t00_sm411_81b6559f}
```

The flag: picoCTF{e_w4y_t00_sm411_81b6559f}