# Redacted RSA 2

## Description

> I have a RSA private key again, but it is partially redacted. Can you recover that?

The following key was provided:

```
-----BEGIN RSA PRIVATE KEY-----
[AWS_SECRET_REMOVED]Awtysp3egIzD+dqGpSLWPIm+
[AWS_SECRET_REMOVED]gmj0AZAEL1DtGiMenKU5a16J
[AWS_SECRET_REMOVED]xF37iit4YlFPk3TFlJK3TWMe
[AWS_SECRET_REMOVED]QQpnVnW/Feo3rq6t/SS0dmbz
[AWS_SECRET_REMOVED]jcOYGPFfpEwlDtjTH7e0KiMX
[AWS_SECRET_REMOVED]LbHkfAze5pX3qlXmXzD4lEyQ
[AWS_SECRET_REMOVED]E83pNch02q6oU5Fl2VFiGJZQ
[AWS_SECRET_REMOVED]frB+Z5n5NZbU5/kHye/v8gh3
[AWS_SECRET_REMOVED]xzs4dzQYuRU93Qd/Zu5rLeUv
[AWS_SECRET_REMOVED]ajEKCvm5CRC/7MM4igsALTUP
[AWS_SECRET_REMOVED]ckEK9OSLUMxtGlDVBj8CAwEA
AQ**************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****[AWS_SECRET_REMOVED]DteqgJrSPRGlAx8hp5Lg
[AWS_SECRET_REMOVED]lV7kei1TPEIxqOMDryFeGGJx
[AWS_SECRET_REMOVED]9lkasZec/1AI9X/TrLIYcjgI
[AWS_SECRET_REMOVED]E3OsqpCYf9Y6eXFnKnInkZ92
[AWS_SECRET_REMOVED]sA3zLJZAgA6JdIiJaV2O5jPJ
ApxJ1OpLoMQ/10knSR76EJBhK2I8LHs*********************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
************************************************************
-----END RSA PRIVATE KEY-----
```

In addition, a ciphertext was provided.

## Solution

This is the second challenge in the "Redacted RSA" series.

Reminder: When we decode an RSA private key in PEM format as ASN.1, we get a structured sequence of 
integers representing different components of the RSA key. 
The ASN.1 structure typically looks like this:

```sql
RSAPrivateKey ::= SEQUENCE {
    version           Version,
    modulus           INTEGER,  -- n
    publicExponent    INTEGER,  -- e
    privateExponent   INTEGER,  -- d
    prime1            INTEGER,  -- p
    prime2            INTEGER,  -- q
    exponent1         INTEGER,  -- d mod (p-1)
    exponent2         INTEGER,  -- d mod (q-1)
    coefficient       INTEGER,  -- (inverse of q) mod p
    otherPrimeInfos   OtherPrimeInfos OPTIONAL
}
```

This time, the unredacted values are split across two locations.

The first part is easy. As before, we can decode the key content as Base64 and push the result to an ASN.1 parser to see what we
have:

```
SEQUENCE
  INTEGER 00
  INTEGER [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]410af4e48b50cc6d1a50d5063f
  INTEGER 010001
```

Based on the expected structure, this is the version, `n` and `e`. But what is the second part?

Judging by the location of the second unredacted text, and using the key from part 1 as reference for the location, it's
safe to assume that we're talking about `exponent2` here. Let's decode it as ASN.1, and get:

```
INTEGER [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]c43fd74927491efa1090612b623c2c7b
```

So, we have `n`, `e`, and `exponent2`. How do we recover the original key?

If we know $d_q$ (or $d_p$), we can potentially recover the factors $p$ and $q$ of $n$, thereby breaking RSA. This is based on the fact that since $d_q \cdot e \equiv 1 \mod (q-1)$, we know that $d_q \cdot e - 1$ is a multiple of $q - 1$. Therefore, we can iterate over divisors of $d_q \cdot e - 1$, and for each divisor $i$, we can compute a possible value for $q = \frac{d_q \cdot e - 1}{i} + 1$. Then, for this $q$ candidate, we calculate $p = \frac{n}{q}$ and check if this yields integer values for both $p$ and $q$. In such a case, the correct factors have been found. (Credit: Based on [this script](https://github.com/yifeng-lee/RSA-In-CTF/blob/master/expa.py)).

The following script implements the attack and creates a full RSA key:

```python
import gmpy2
from Crypto.PublicKey import RSA

e = 0x10001
n = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]72410af4e48b50cc6d1a50d5063f
dq = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]a0c43fd74927491efa1090612b623c2c7b

for i in range(1, e + 1):
    if (dq * e - 1) % i == 0:
        q = ( (dq * e - 1) // i) + 1
        if n % q == 0:
            p = n // q
            phi = (q - 1) * (p - 1)
            d = gmpy2.invert(e, phi) % phi
            print(f"Found d: {d}")
            key = RSA.construct((n, e, int(d)))
            with open('private_key.pem', 'wb') as f:
                f.write(key.export_key('PEM'))
            break
```

Now we can use the key to decode the ciphertext:

```console
┌──(user@kali3)-[/media/sf_CTFs/google/Redacted_RSA_2]
└─$ openssl pkeyutl -decrypt -inkey private_key.pem -in encrypted.txt -out -
CTF{rsa_attacks_usually_involves_modulo_arithmetic}
```

