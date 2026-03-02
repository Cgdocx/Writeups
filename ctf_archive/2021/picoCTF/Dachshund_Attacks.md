# Dachshund Attacks
Category: Crypto, 80 points

## Description
> What if d is too small?

## Solution

Let's connect to the attached service:

```console
┌──(user@kali)-[/media/sf_CTFs/pico/Dachshund_Attacks]
└─$ nc mercury.picoctf.net 31133
Welcome to my RSA challenge!
e: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]8659371101425091253226312809
n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]0214048887523664730897405647
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]2390747204250124554416009841
```

The description says that a small `d` value is used. This, and the challenge title, point towards [Wiener's attack](https://en.wikipedia.org/wiki/Wiener%27s_attack).

```console
┌──(user@kali)-[/media/sf_CTFs/pico/Dachshund_Attacks]
└─$ python3 ~/utils/crypto/RsaCtfTool/RsaCtfTool.py -n [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]0214048887523664730897405647 -e [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]8659371101425091253226312809 --uncipher [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]2390747204250124554416009841 --attack wiener
private argument is not set, the private key will not be displayed, even if recovered.

[*] Testing key /tmp/tmp_mbmx4ue.
[*] Performing wiener attack on /tmp/tmp_mbmx4ue.
100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 619/619 [00:01<00:00, 551.94it/s]
 25%|█████████████████████████████████████████▎                                                                                                                            | 154/619 [00:00<00:00, 1819.71it/s]
[*] Attack success with wiener method !

Results for /tmp/tmp_mbmx4ue:

Unciphered data :
HEX : [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]5f313134363038347d
INT (big endian) : [AWS_SECRET_REMOVED]08045477674049828293333354607555709
INT (little endian) : [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]2453301138070119563546066944
STR : b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00picoCTF{proving_wiener_1146084}'
```

The flag: `picoCTF{proving_wiener_1146084}`.