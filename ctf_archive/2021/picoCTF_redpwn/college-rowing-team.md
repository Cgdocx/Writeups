# college-rowing-team

## Description

> I just joined my college's rowing team! To make a good first impression, I started sending my teammates positive automated messages every day. I even send them flags from time to time!

Two files were attached:

```python
#!/usr/bin/env python3

import random
from Crypto.Util.number import getPrime, bytes_to_long

with open('flag.txt', 'rb') as f:
    flag = f.read()

msgs = [
    b'I just cannot wait for rowing practice today!',
    b'I hope we win that big rowing match next week!',
    b'Rowing is such a fun sport!'
        ]

msgs.append(flag)
msgs *= 3
random.shuffle(msgs)

for msg in msgs:
    p = getPrime(1024)
    q = getPrime(1024)
    n = p * q
    e = 3
    m = bytes_to_long(msg)
    c = pow(m, e, n)
    with open('encrypted-messages.txt', 'a') as f:
        f.write(f'n: {n}\n')
        f.write(f'e: {e}\n')
        f.write(f'c: {c}\n\n')
```

```
n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]49100863883977473
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]52544599393

n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]33589471220112129
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]617292433784471465084923195909989

n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]78926442519736239
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]617292433784471465084923195909989

n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]77770449028735883
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]52544599393

n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]20864728015961207
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]4401

n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]84278617562166603
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]4567478138877010606365500800690273

n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]19542893405128843
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]4567478138877010606365500800690273

n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]70297510077861213
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]4567478138877010606365500800690273

n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]54248157720756807
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]4401

n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]76165684240183111
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]52544599393

n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]12204167653199743
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]4401

n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]23281163075118193
e: 3
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]617292433784471465084923195909989


```

## Solution

We can see that together with the flag, we have three more plaintext messages that are encrypted:

```python
    b'I just cannot wait for rowing practice today!',
    b'I hope we win that big rowing match next week!',
    b'Rowing is such a fun sport!'
```

We get all the encryption results but we don't know which ciphertext corresponds to which plaintext.

The challenge name hints towards using the Chinese remainder theorem. The simplest way to automatically solve this challenge is to use `RsaCtfTool` on the ciphertexts until it decrypts the flag:

```console
┌──(user@kali)-[/media/sf_CTFs/pico/college-rowing-team]
└─$ ~/utils/crypto/RsaCtfTool/RsaCtfTool.py -n [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]33589471220112129 -e 3 --uncipher [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]617292433784471465084923195909989 
private argument is not set, the private key will not be displayed, even if recovered.

[*] Testing key /tmp/tmppdy7hjc0.
[*] Performing factordb attack on /tmp/tmppdy7hjc0.
[*] Performing mersenne_primes attack on /tmp/tmppdy7hjc0.
100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 51/51 [00:05<00:00,  9.68it/s]
[*] Performing smallq attack on /tmp/tmppdy7hjc0.
[*] Performing fibonacci_gcd attack on /tmp/tmppdy7hjc0.
100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 9999/9999 [00:00<00:00, 26631.56it/s]
[*] Performing system_primes_gcd attack on /tmp/tmppdy7hjc0.
100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 7007/7007 [00:00<00:00, 103281.91it/s]
[*] Performing pastctfprimes attack on /tmp/tmppdy7hjc0.
100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 113/113 [00:00<00:00, 183704.01it/s]
[*] Performing cube_root attack on /tmp/tmppdy7hjc0.
[*] Performing ecm2 attack on /tmp/tmppdy7hjc0.
[*] Performing wiener attack on /tmp/tmppdy7hjc0.
100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 3/3 [00:00<00:00, 56679.78it/s]
100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 3/3 [00:00<00:00, 82782.32it/s]
[*] Performing z3_solver attack on /tmp/tmppdy7hjc0.
[*] Performing partial_q attack on /tmp/tmppdy7hjc0.
[*] Performing mersenne_pm1_gcd attack on /tmp/tmppdy7hjc0.
100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 2045/2045 [00:00<00:00, 21196.81it/s]
[*] Performing SQUFOF attack on /tmp/tmppdy7hjc0.
[!] Timeout.
[*] Performing qicheng attack on /tmp/tmppdy7hjc0.
[*] Performing primorial_pm1_gcd attack on /tmp/tmppdy7hjc0.
100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 10000/10000 [00:02<00:00, 3962.22it/s]
[*] Performing wolframalpha attack on /tmp/tmppdy7hjc0.
[*] Performing noveltyprimes attack on /tmp/tmppdy7hjc0.
100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 21/21 [00:00<00:00, 71262.45it/s]
[*] Performing fermat attack on /tmp/tmppdy7hjc0.
[!] Timeout.
[*] Performing small_crt_exp attack on /tmp/tmppdy7hjc0.
[*] Performing boneh_durfee attack on /tmp/tmppdy7hjc0.
[*] Performing pollard_p_1 attack on /tmp/tmppdy7hjc0.
  0%|                                                                                                                                                                                  | 0/168 [00:01<?, ?it/s]
[*] Performing comfact_cn attack on /tmp/tmppdy7hjc0.
[*] Performing fermat_numbers_gcd attack on /tmp/tmppdy7hjc0.
  0%|▍                                                                                                                                                                     | 30/9999 [00:32<3:51:18,  1.39s/it][!] Timeout.
  0%|▍                                                                                                                                                                     | 30/9999 [01:00<5:32:20,  2.00s/it]
[*] Performing siqs attack on /tmp/tmppdy7hjc0.
[*] Performing pollard_rho attack on /tmp/tmppdy7hjc0.
[!] Timeout.
[*] Performing roca attack on /tmp/tmppdy7hjc0.
[-] This key is not roca, skiping test...
[*] Performing londahl attack on /tmp/tmppdy7hjc0.
 26%|████████████████████████████████████████                                                                                                                   | 5166294/20000001 [00:50<2:40:36, 1539.37it/s][!] Timeout.
 26%|████████████████████████████████████████▎                                                                                                                   | 5166295/20000001 [01:27<04:10, 59207.86it/s]
[*] Performing cm_factor attack on /tmp/tmppdy7hjc0.
100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 6/6 [00:03<00:00,  1.74it/s]
[*] Performing neca attack on /tmp/tmppdy7hjc0.
Can't load neca because neca binary is not installed
[*] Performing euler attack on /tmp/tmppdy7hjc0.
[!] Timeout.
[*] Performing smallfraction attack on /tmp/tmppdy7hjc0.
[*] Performing binary_polinomial_factoring attack on /tmp/tmppdy7hjc0.
[*] Performing ecm attack on /tmp/tmppdy7hjc0.

Results for /tmp/tmppdy7hjc0:

Unciphered data :
HEX : [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]5f345f72333473306e7d
INT (big endian) : [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]47026455386576187267483573457800687229
INT (little endian) : [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]69635423492169717200393229464087259504
STR : b'picoCTF{1_gu3ss_p30pl3_p4d_m3ss4g3s_f0r_4_r34s0n}'
```