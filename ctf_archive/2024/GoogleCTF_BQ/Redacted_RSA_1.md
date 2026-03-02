# Redacted RSA 1

## Description

> I have a RSA private key, but it is partially redacted. Can you recover that? Run `openssl pkeyutl -decrypt -inkey key-recovered.pem -in encrypted.txt -out plaintext.txt` after you have recovered the key.

The following key was provided:

```
-----BEGIN RSA PRIVATE KEY-----
[AWS_SECRET_REMOVED]DjAr3ow9bzFGF/aoxfF+Rbdr
[AWS_SECRET_REMOVED]XHAOoB4KwsJ2IdX67aGZT9A3
[AWS_SECRET_REMOVED]aeRUneiE+OLhS7YjaBY6YcVl
[AWS_SECRET_REMOVED]eXggyrEakFKtfAusojHmAbIE
[AWS_SECRET_REMOVED]VYu9w/tKc2eG5Sys2Lx/0alz
[AWS_SECRET_REMOVED]YTZE9PFmyAOenjnEXvlo4Jut
[AWS_SECRET_REMOVED]c06dgCJdb8Q7vn0D6n5P5zgT
[AWS_SECRET_REMOVED]Uw9miydHe1E7I+9AtVGCVNC3
[AWS_SECRET_REMOVED]I/O3SFmRLGOYJfa9Z+wX6mym
[AWS_SECRET_REMOVED]y6KyzghZP31gfao3/kzeHA0G
[AWS_SECRET_REMOVED]WY4iprimn/y6XpyHYNsCAwEA
[AWS_SECRET_REMOVED]iq5d1doSDnjDtC4DPnuNOSXY
[AWS_SECRET_REMOVED]ztdehUYqaLGNwiT8hQG+RLmf
[AWS_SECRET_REMOVED]jdG4jI+84LQz50myMk2m8rB+
[AWS_SECRET_REMOVED]SRU/NMnmEWtt02HhdOkRJbO/
[AWS_SECRET_REMOVED]yNoYkd7gOMNigx9oINUh18jb
[AWS_SECRET_REMOVED]BwoIResB1+tpGOW72QFQVQKL
[AWS_SECRET_REMOVED]TAZJkvGO6jvPuzmMP26YJrle
[AWS_SECRET_REMOVED]oXNjinmyoFMYYnZGdqbq4CJR
[AWS_SECRET_REMOVED]DggtHZ65knyyQUglrM2JXzr1
[AWS_SECRET_REMOVED]8OmPdj1O9BuoKk0UGLQbWS0/
[AWS_SECRET_REMOVED]dcVoauflJQ**************
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
************************************************************
-----END RSA PRIVATE KEY-----
```

In addition, a ciphertext was provided.

## Solution

When we decode an RSA private key in PEM format as ASN.1, we get a structured sequence of 
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

In our case, we have the content of the key up to some point, but without some of the members.

We can decode the key content as Base64 and push the result to an ASN.1 parser to see what we
have:

```
SEQUENCE
  INTEGER 00
  INTEGER [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]8e22a6b8a69ffcba5e9c8760db
  INTEGER 010001
  INTEGER [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]58455d3e7775c5686ae7e525
```

This gives us the version, `n`, `e` and `d`, which happen to be enough to reconstruct the full key.

Let's do that:

```python
>>> from Crypto.PublicKey import RSA
>>> n = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]598e22a6b8a69ffcba5e9c8760db
>>> e = 0x010001
>>> d = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]7f58455d3e7775c5686ae7e525
>>> key = RSA.construct((n, e, d))
>>> with open('private_key.pem', 'wb') as f:
...     f.write(key.export_key('PEM'))
...
3242
```

The full key is:

```
-----BEGIN RSA PRIVATE KEY-----
[AWS_SECRET_REMOVED]DjAr3ow9bzFGF/aoxfF+Rbdr
[AWS_SECRET_REMOVED]XHAOoB4KwsJ2IdX67aGZT9A3
[AWS_SECRET_REMOVED]aeRUneiE+OLhS7YjaBY6YcVl
[AWS_SECRET_REMOVED]eXggyrEakFKtfAusojHmAbIE
[AWS_SECRET_REMOVED]VYu9w/tKc2eG5Sys2Lx/0alz
[AWS_SECRET_REMOVED]YTZE9PFmyAOenjnEXvlo4Jut
[AWS_SECRET_REMOVED]c06dgCJdb8Q7vn0D6n5P5zgT
[AWS_SECRET_REMOVED]Uw9miydHe1E7I+9AtVGCVNC3
[AWS_SECRET_REMOVED]I/O3SFmRLGOYJfa9Z+wX6mym
[AWS_SECRET_REMOVED]y6KyzghZP31gfao3/kzeHA0G
[AWS_SECRET_REMOVED]WY4iprimn/y6XpyHYNsCAwEA
[AWS_SECRET_REMOVED]iq5d1doSDnjDtC4DPnuNOSXY
[AWS_SECRET_REMOVED]ztdehUYqaLGNwiT8hQG+RLmf
[AWS_SECRET_REMOVED]jdG4jI+84LQz50myMk2m8rB+
[AWS_SECRET_REMOVED]SRU/NMnmEWtt02HhdOkRJbO/
[AWS_SECRET_REMOVED]yNoYkd7gOMNigx9oINUh18jb
[AWS_SECRET_REMOVED]BwoIResB1+tpGOW72QFQVQKL
[AWS_SECRET_REMOVED]TAZJkvGO6jvPuzmMP26YJrle
[AWS_SECRET_REMOVED]oXNjinmyoFMYYnZGdqbq4CJR
[AWS_SECRET_REMOVED]DggtHZ65knyyQUglrM2JXzr1
[AWS_SECRET_REMOVED]8OmPdj1O9BuoKk0UGLQbWS0/
[AWS_SECRET_REMOVED]dcVoauflJQKCAQEAw32wGTGI
[AWS_SECRET_REMOVED]xZik/VPXKNnbQhxjrDhAPIlC
[AWS_SECRET_REMOVED]sWLoxfQwJD5rAx0gw+oDgE4y
[AWS_SECRET_REMOVED]zQTrQGk/gIWBTxJRAbMhDjXS
[AWS_SECRET_REMOVED]mWBhcqk0KvhbYSC14ltHhlxL
[AWS_SECRET_REMOVED]G226VGQXW2+bClcUJXlrzzz9
[AWS_SECRET_REMOVED]9RMt2e2CGZnKmcuJE3fjdvsr
[AWS_SECRET_REMOVED]oFtCdgVvBHgeb029VIxJyqsu
[AWS_SECRET_REMOVED]C3rdrdfalQXZbpxp2fVPGUEC
[AWS_SECRET_REMOVED]+WdKaurw5wfOBKYz0CQaGD6G
[AWS_SECRET_REMOVED]hg2CtvIKhwd1gu2vJ0T0S+dE
[AWS_SECRET_REMOVED]zwKCAQEApGsBwS64MLkNVqK6
[AWS_SECRET_REMOVED]Q313u5LFAgQNgtyupSMzDCSu
[AWS_SECRET_REMOVED]/LvCpUvKUvxvFWqvubWKJXtk
[AWS_SECRET_REMOVED]fDTNHQP6vcM6+giqyXo99zfL
[AWS_SECRET_REMOVED]bUeVE4u/bjH3ZjoZokSwfHls
[AWS_SECRET_REMOVED]Uyb67ydt+la1JgfV8Fe/CCJW
[AWS_SECRET_REMOVED]kw9Q7qMg5I8o4UsOJaTpXuBA
[AWS_SECRET_REMOVED]2OoqTU0mjVkzO/WcT1dtmRUs
[AWS_SECRET_REMOVED]HdFxJR3Sni+WoY/3HGQQSbvW
[AWS_SECRET_REMOVED]6ttB4qteEd6SvDglxKLFTX4K
[AWS_SECRET_REMOVED]byDMDwwRFgdVxY0PPFUALwiH
[AWS_SECRET_REMOVED]7UB9tOnsGrQkN8YkijQSqsyL
[AWS_SECRET_REMOVED]WFJwGNsZe1ODVsMnpLWV3HF6
[AWS_SECRET_REMOVED]TZjpoYd3FG2q0e+awyJ5F1AR
[AWS_SECRET_REMOVED]Vn9Kr4yQpamZwyjKrTl98T1n
[AWS_SECRET_REMOVED]J8JWM3kv5fHg8f+5z9dvphTM
[AWS_SECRET_REMOVED]0FjWeaYo2WqQulAt8NME
-----END RSA PRIVATE KEY-----
```

At this point we can fully parse the key to list its different components. While this 
is not needed for the current challenge, it will help us with the upcoming two challenges.

```console
┌──(user@kali)-[/media/sf_CTFs/google/Redacted_RSA_1]
└─$ openssl asn1parse -in private_key.pem -i
    0:d=0  hl=4 l=2345 cons: SEQUENCE
    4:d=1  hl=2 l=   1 prim:  INTEGER           :00
    7:d=1  hl=4 l= 513 prim:  INTEGER           :[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]22A6B8A69FFCBA5E9C8760DB
  524:d=1  hl=2 l=   3 prim:  INTEGER           :010001
  529:d=1  hl=4 l= 512 prim:  INTEGER           :[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]58455D3E7775C5686AE7E525
 1045:d=1  hl=4 l= 257 prim:  INTEGER           :[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]25796BCF3CFD82ECAD24A437F31C8035
 1306:d=1  hl=4 l= 257 prim:  INTEGER           :[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]E01AAAD3FA41F92B39560BF07D4F9ECF
 1567:d=1  hl=4 l= 257 prim:  INTEGER           :[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]FA56B52607D5F057BF0822562C52BC41
 1828:d=1  hl=4 l= 256 prim:  INTEGER           :[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]3D9C6CD6595AEED1A1430F00325A136F
 2088:d=1  hl=4 l= 257 prim:  INTEGER           :[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]D2D058D679A628D96A90BA502DF0D304
 
```

Finally, we can decrypt the ciphertext:

```console
┌──(user@kali)-[/media/sf_CTFs/google/Redacted_RSA_1]
└─$ openssl pkeyutl -decrypt -inkey private_key.pem -in encrypted.txt -out plaintext.txt

┌──(user@kali)-[/media/sf_CTFs/google/Redacted_RSA_1]
└─$ cat plaintext.txt
CTF{learning_the_der_encoding_helps}
```