# b00tl3gRSA2
Cryptography, 400 points

## Description:
> In RSA d is alot bigger than e, why dont we use d to encrypt instead of e?


## Solution: 

This challenge is similar to last year's [Super Safe RSA 2](/2018_picoCTF/Super%20Safe%20RSA%202.md).

Let's connect to the attached service:

```console
root@kali:/media/sf_CTFs/pico/b00tl3gRSA2# nc 2019shell1.picoctf.com 25894
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]7669664957903314187967291512
n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]6341614902994131107305595311
e: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]9514843888968859835457405377
```

We see that `e` is very large, and the description suggests that `d` is much smaller (i.e. `d` and `e` were swapped).

The following two posts summarize why this is a bad idea:

* [RSA: Does it matter if you use e or d to encrypt?](https://crypto.stackexchange.com/questions/54557/rsa-does-it-matter-if-you-use-e-or-d-to-encrypt)
* [Why RSA uses {d,n} as private key instead of {e,n}?](https://crypto.stackexchange.com/questions/8460/why-rsa-uses-d-n-as-private-key-instead-of-e-n)

In short, if the secret exponent (usually denoted as `d`) is small, it is vulnerable to Wiener's attack.

We'll use `RsaCtfTool` to trigger the attack.

First, we create a PKCS#8 Public Key file from `e` and `n`, since that's the format the tool works with:
```console
root@kali:/media/sf_CTFs/pico/b00tl3gRSA2# python ~/utils/RsaCtfTool/RsaCtfTool.py --createpub  --e [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]9514843888968859835457405377  --n [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]6341614902994131107305595311 > key.pub
root@kali:/media/sf_CTFs/pico/b00tl3gRSA2# cat key.pub
-----BEGIN PUBLIC KEY-----
[AWS_SECRET_REMOVED]gQCDMbUe0lvVv2oOZShmsIgx
[AWS_SECRET_REMOVED]OMPa1W+rsDNj60jGjyU/RbnU
[AWS_SECRET_REMOVED]KMZAvq997n29lSJjCBa0Vre4
[AWS_SECRET_REMOVED]gwX0kwzWEuyflYJTLF1gaRah
[AWS_SECRET_REMOVED]FbxkryA7mUnGJjlPnfklediL
[AWS_SECRET_REMOVED]rss/jWdzvVQ7CSLsP3TmksYP
VQHB
-----END PUBLIC KEY-----
```

Then we convert the ciphertext to binary:
```console
root@kali:/media/sf_CTFs/pico/b00tl3gRSA2# c=[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]7669664957903314187967291512
root@kali:/media/sf_CTFs/pico/b00tl3gRSA2# echo "obase=16; $c" | BC_LINE_LENGTH=0 bc | awk '{ print (length($0) % 2 == 0) ? $0 : 0$0; }' | xxd -p -r > c.bin
root@kali:/media/sf_CTFs/pico/b00tl3gRSA2# xxd -g 1 c.bin
00000000: 44 74 b6 98 70 33 7d 78 5d ca 5a dc 2c ea c3 a5  Dt..p3}x].Z.,...
00000010: 6c 38 1c b0 27 15 67 a5 19 ce 19 1c eb 57 81 1e  l8..'.g......W..
00000020: ab cd 62 e1 11 c9 af 88 ce 1a ef 27 cc 2d eb 28  ..b........'.-.(
00000030: 60 39 27 8e 08 2c 9b 3f d3 31 b5 fd 2e 70 c1 cb  `9'..,.?.1...p..
00000040: 51 ec 6a bb 05 8e 0b 30 33 42 ad 77 61 4b cd 3f  Q.j....03B.waK.?
00000050: 43 78 15 21 c3 43 7f c8 14 84 ab 03 e4 97 ca d3  Cx.!.C..........
00000060: 19 10 37 ea db f4 9e 1d 0d 8a 52 da e8 df 2f 83  ..7.......R.../.
00000070: 84 b3 45 3d 01 ec e9 59 52 9d d0 ff 23 30 60 78  ..E=...YR...#0`x
```

Finally, we trigger the tool to uncipher the text:
```console
root@kali:/media/sf_CTFs/pico/b00tl3gRSA2# python ~/utils/RsaCtfTool/RsaCtfTool.py  --publickey key.pub --private --uncipher c.bin  --verbose
[*] Performing hastads attack.
[*] Performing factordb attack.
[*] Performing pastctfprimes attack.
[*] Loaded 71 primes
[*] Performing noveltyprimes attack.
[*] Performing smallq attack.
[*] Performing wiener attack.
-----BEGIN RSA PRIVATE KEY-----
[AWS_SECRET_REMOVED]cSGBCWjpXcSdZsjB+FEPY6EU
[AWS_SECRET_REMOVED]VXkLEp/EV+sLbhu0QIGaYYiv
[AWS_SECRET_REMOVED]DfLxu8j+YCHDyjWhrwKBgFFn
[AWS_SECRET_REMOVED]65j44IFq5abnBJCCtmnNhvjJ
[AWS_SECRET_REMOVED]snR9wpC6w3Hp5x2vQLJpx2hZ
[AWS_SECRET_REMOVED]AgMBAAECQQCgeR9tnH4TLS3e
[AWS_SECRET_REMOVED]ogA9e6rSLSy6FBIuiOLd54WW
[AWS_SECRET_REMOVED]GFvjgxcP+WF39uYDPmzhACq+
[AWS_SECRET_REMOVED]AgMBAAECQE9tBqDnKy8O+uw8
[AWS_SECRET_REMOVED]wxcaeZrx152ob/EMcNFrSMq8
eEN9+D8=
-----END RSA PRIVATE KEY-----
[+] Clear text : picoCTF{bad_1d3a5_3468581}
```