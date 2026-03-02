# Super Safe RSA 2
Cryptography, 425 points

## Description:
> Wow, he made the exponent really large so the encryption MUST be safe, right?! 

## Solution:

After connecting to the server, we receive a response similar to the the following:
```console
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA_2# nc 2018shell3.picoctf.com 56543
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]128217332674385161736097955
n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]6835967585096552443891341599
e: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]9088903977960710315151546097
```

We'll solve the challenge with [RsaCtfTool](https://github.com/Ganapati/RsaCtfTool).

First, we create a public key from n and e:
```console
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA_2# ~/utils/RsaCtfTool/RsaCtfTool.py --createpub --n [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]6835967585096552443891341599 --e [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]9088903977960710315151546097 > key.pub
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA_2# cat key.pub
-----BEGIN PUBLIC KEY-----
[AWS_SECRET_REMOVED]gFJdaCxlX7nVDA9fq7Ob11Bd
[AWS_SECRET_REMOVED]POjYZNCABBywgckh4sjSgcuQ
[AWS_SECRET_REMOVED]ETwWzSbamfO9gX5wE9466tD0
[AWS_SECRET_REMOVED]+yz5snGA3MGCSoZWL8YcDj/i
[AWS_SECRET_REMOVED]1lvmoRESoqq/aC7lDqOJTLc7
[AWS_SECRET_REMOVED]L1Cb5Dildm4eVnb2pfCQVxWt
VvE=
-----END PUBLIC KEY-----
```

We check the RsaCtfTool can crack the private key:
```console
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA_2# ~/utils/RsaCtfTool/RsaCtfTool.py --publickey key.pub --private > key.priv
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA_2# cat key.priv
-----BEGIN RSA PRIVATE KEY-----
[AWS_SECRET_REMOVED]arCtX4uNz6wF0QeSwkgv3DLK
[AWS_SECRET_REMOVED]uZ5BbVM8QzvpZJ1QgFt4Wzfe
[AWS_SECRET_REMOVED]Q9QLygxTTvrtOtkfAoGAHQKP
[AWS_SECRET_REMOVED]EFvjF/R0AbUF1gH3Hpd29KA7
[AWS_SECRET_REMOVED]bGPoS3WOiHAy5oGPqLDKJq7f
[AWS_SECRET_REMOVED]AwEAAQJBAI2jxQtLNY+u0bgk
[AWS_SECRET_REMOVED]0noMjDByJ9qG+qKTOmlkUESK
[AWS_SECRET_REMOVED]2TBknc5P5ENLRNgvc9uk4EsC
[AWS_SECRET_REMOVED]AwEAAQJAf2GGSwhjPTGjcCtV
[AWS_SECRET_REMOVED]Zwd62KdooIIcfi/NxcIAfYLO
snB9Ig==
-----END RSA PRIVATE KEY-----
``

We save c as a binary file:
```console
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA_2# c=[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]128217332674385161736097955
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA_2# echo "obase=16; $c" | BC_LINE_LENGTH=0 bc | awk '{ print (length($0) % 2 == 0) ? $0 : 0$0; }' | xxd -p -r > c.bin
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA_2# xxd c.bin
00000000: 0493 5935 df50 2c5c bcee 0261 abdf 0b77  ..Y5.P,\...a...w
00000010: 0a2d 6c8b eec1 5fc9 2e8a 4dbd 0d31 6419  .-l..._...M..1d.
00000020: 0266 cbe0 fdc5 31a5 6f9a 4fbb 27dc 7418  .f....1.o.O.'.t.
00000030: 593d 4fec 22b8 f818 23b1 af12 e9c0 1959  Y=O."...#......Y
00000040: 45fa 1005 340a e7ee 3d90 a0d6 d14f 636f  E...4...=....Oco
00000050: fc32 66da 38e8 303f d0a8 5bad f10a 0baa  .2f.8.0?..[.....
00000060: e882 d7b2 59eb ac94 d628 3e0a 57b2 173f  ....Y....(>.W..?
00000070: fdb6 2baa b7aa cedd eedb 1f50 8a63 10a3  ..+........P.c..
```

Finally, we run RsaCtfTool to decrypt the ciphertext:
```console
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA_2# ~/utils/RsaCtfTool/RsaCtfTool.py --publickey key.pub --private --uncipher c.bin
-----BEGIN RSA PRIVATE KEY-----
[AWS_SECRET_REMOVED]arCtX4uNz6wF0QeSwkgv3DLK
[AWS_SECRET_REMOVED]uZ5BbVM8QzvpZJ1QgFt4Wzfe
[AWS_SECRET_REMOVED]Q9QLygxTTvrtOtkfAoGAHQKP
[AWS_SECRET_REMOVED]EFvjF/R0AbUF1gH3Hpd29KA7
[AWS_SECRET_REMOVED]bGPoS3WOiHAy5oGPqLDKJq7f
[AWS_SECRET_REMOVED]AwEAAQJBAI2jxQtLNY+u0bgk
[AWS_SECRET_REMOVED]0noMjDByJ9qG+qKTOmlkUESK
[AWS_SECRET_REMOVED]2TBknc5P5ENLRNgvc9uk4EsC
[AWS_SECRET_REMOVED]AwEAAQJAf2GGSwhjPTGjcCtV
[AWS_SECRET_REMOVED]Zwd62KdooIIcfi/NxcIAfYLO
snB9Ig==
-----END RSA PRIVATE KEY-----
[+] Clear text : picoCTF{w@tch_y0ur_Xp0n3nt$_c@r3fu11y_4137999}
```

The flag: picoCTF{w@tch_y0ur_Xp0n3nt$_c@r3fu11y_4137999}