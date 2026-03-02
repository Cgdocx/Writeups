# Redacted RSA 3

## Description

> I have a RSA private key again and again, but it is partially redacted. Can you recover that?

The following key was provided:

```
-----BEGIN RSA PRIVATE KEY-----
[AWS_SECRET_REMOVED]R973/Sbtkcsq22GxTJsFYfS8
[AWS_SECRET_REMOVED]hLq1KNgCGjASTrPv8w17ogvU
[AWS_SECRET_REMOVED]bLzqyS991Mt30ysNyqX3Xu+m
[AWS_SECRET_REMOVED]ZkKVoUlxNO/L089j/eatStc+
[AWS_SECRET_REMOVED]JuU+mxdAqN1iGYCXSv9CzL8Z
[AWS_SECRET_REMOVED]47eyE7w7SMcFsQQzMRu7+Fmm
[AWS_SECRET_REMOVED]1o1o+uMj6CDuS1F2LKWt9U8A
[AWS_SECRET_REMOVED]HdOf5L1K0DZu7KIvEyOXcd5K
[AWS_SECRET_REMOVED]BbANyRaWETiJi0Vj/5MPYgB/
[AWS_SECRET_REMOVED]fFc+P/nktBKQ7ctLUWJaarM5
[AWS_SECRET_REMOVED]JeqE4K1pb1g4eyq1FSsCAwEA
AQKCAgA2********************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
************************************************QQKCAQEAvp0qAvuY
[AWS_SECRET_REMOVED]pQtjpWjz7yWQ35GR9eyWb4n+
[AWS_SECRET_REMOVED]TD6k8WSpsHJVObP/lB+HiW+4
[AWS_SECRET_REMOVED]9/NynJRGw48fARJh71u5/pUK
[AWS_SECRET_REMOVED]iiMp0v2pg7jsmoDtQdjSz9jx
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
************************************************************
-----END RSA PRIVATE KEY-----
```

In addition, a ciphertext was provided.

## Solution

This is the third challenge in the "Redacted RSA" series.

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

Again, the unredacted values are split across two locations.

The first part is easy. As before, we can decode the key content as Base64 and push the result to an ASN.1 parser to see what we
have:

```
SEQUENCE
  INTEGER 00
  INTEGER [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]ea84e0ad696f58387b2ab5152b
  INTEGER 010001
  INTEGER 36
```

Based on the expected structure, this is the version, `n` and `e` (and the very first byte of `d`). But what is the second part?

Judging by the location of the second unredacted text, and using the key from part 1 as reference for the location,
it looks like this is a partial `p` value. Since it's partial, it won't get decoded cleanly by an
ASN.1 parser, but we can decode it using base64 and manually parse the ASN.1:

```
41 02 82 01 01 00 be 9d 2a 02 fb 98 16 a6 df cf b4 92 f5 e3 84 be dd 00 bf 8f 52 06 21 b2 27 ed 3b 24 f4 48 dd 13 69 57 60 df a5 0b 63 a5 68 f3 ef 25 90 df 91 91 f5 ec 96 6f 89 fe 31 c5 1e cc 65 59 1b 1d f8 df c9 6c 10 5e 68 c7 97 e2 60 20 fd 95 e6 b6 a7 93 8b fe c1 cb 4c 3e a4 f1 64 a9 b0 72 55 39 b3 ff 94 1f 87 89 6f b8 d2 36 e0 f9 fd d5 f0 2c 8e c2 6e ed 12 17 46 5a e4 02 96 2d 0f 1a b6 e9 f1 23 06 41 20 bc f7 f3 72 9c 94 46 c3 8f 1f 01 12 61 ef 5b b9 fe 95 0a 45 73 8d 62 a1 d1 80 0a 41 6a 93 e9 9e 18 ed 56 0e 3f b5 5f 4b b2 f1 3f b8 ae 02 e9 06 c9 8a 23 29 d2 fd a9 83 b8 ec 9a 80 ed 41 d8 d2 cf d8 f1
```

That `41` looks like a remainder from the previous integer, based on the fact that the following 4 bytes decode nicely as meaningful ASN.1 in the context:

 * `02`: This is the tag for an integer in ASN.1.
 * `82`: This is the length descriptor, indicating that the length of the integer is specified in the next two bytes.
 * `01 01`: This is the length of the integer in bytes, which is 0x0101 (or 257 in decimal).

The remainder is `199` bytes of `p`. We need to somehow find the remaining `58`. Well, 
clearly there's an attack for that, and I found a reference [here](https://github.com/yifeng-lee/RSA-In-CTF/blob/master/exp6.sage).

```python
e = 0x010001

n = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]25ea84e0ad696f58387b2ab5152b

p = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]000000000000000000000000000000000000

beta = 0.5
epsilon = beta^2/7

pbits = p.nbits()
kbits = floor(n.nbits() * (beta ^ 2-epsilon))
pbar = p & (2 ^ pbits - 2 ^ kbits)
print ("Upper %d bits (of %d bits) are given" % (pbits - kbits, pbits))

PR.<x> = PolynomialRing(Zmod(n))
f = x + pbar

print ('p_fake =', p)
x0 = f.small_roots(X = 2 ^ kbits, beta = 0.3)[0]  # find root < 2^kbits with factor >= n^0.3
new_p = x0 + pbar
print ('p =', new_p)
```

Running it with sage, we get:

```console
┌──(user@kali3)-[/media/sf_CTFs/google/Redacted_RSA_3]
└─$ sage solve.sage
Upper 1171 bits (of 2048 bits) are given
p_fake = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]38913681255301120
p = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]48361887919027809
```

We can now calculate `d` and create a full key using:

```python
>>> import gmpy2
>>> e = 0x010001
>>> n = gmpy2.mpz([AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]25ea84e0ad696f58387b2ab5152b)
>>> p = gmpy2.mpz([AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]48361887919027809)
>>> q = gmpy2.divexact(n, p)
>>> phi_n = (p - 1) * (q - 1)
>>> d = gmpy2.invert(e, phi_n)
>>> from Crypto.PublicKey import RSA
>>> key = RSA.construct((int(n), e, int(d)))
>>> with open('private_key.pem', 'wb') as f:
...     f.write(key.export_key('PEM'))
...
3242
```

Using the key, we decrypt the ciphertext:

```console
┌──(user@kali3)-[/media/sf_CTFs/google/Redacted_RSA_3]
└─$ openssl pkeyutl -decrypt -inkey private_key.pem -in encrypted.txt -out -
CTF{lattices_are_frequently_involved_when_recovering_full_secret_from_partial_ones}
```