# No Padding, No Problem
Category: Crypto, 90 points

## Description

> Oracles can be your best friend, they will decrypt anything, except the flag's ciphertext. How will you break it?

## Solution

Let's connect to the attached server:

```console
┌──(user@kali)-[/media/sf_CTFs/pico/No_Padding_No_Problem]
└─$ nc mercury.picoctf.net 30048
Welcome to the Padding Oracle Challenge
This oracle will take anything you give it and decrypt using RSA. It will not accept the ciphertext with the secret message... Good Luck!


n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]7774179610963694893894058993
e: 65537
ciphertext: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]814932262615707105931438936


Give me ciphertext to decrypt: 
```

We get some RSA values and the ability to decrypt any ciphertext... Except for the provided ciphertext, that is:

```
Give me ciphertext to decrypt: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]814932262615707105931438936
Will not decrypt the ciphertext. Try Again
```

Now, unpadded RSA is [homomorphic](https://en.wikipedia.org/wiki/Homomorphic_encryption), which means that:

```
encrypt(m1) * encrypt(m2) = ((m1**e) * (m2**e)) mod n = (m1 * m2)**e mod n = encrypt(m1 * m2)
```

So, we can take a known value such as `2`, and encrypt it by performing `2**e mod n`.

```python
>>> n = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]7774179610963694893894058993
>>> e = 65537
>>> c = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]814932262615707105931438936
>>> x = pow(2, e, n)
```

Now we can calculate `c * x`:

```python
>>> c * x
[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]704463675796224
```

As explained earlier, `c * x = encrypt(m) * encrypt(2) = encrypt(m * 2)`.

Let's try to decrypt that:

```
Give me ciphertext to decrypt: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]704463675796224
Here you go: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]930
```

We got `m * 2`. We take the result and divide back by `2` to get `m`:

```python
>>> m = [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]930 // 2
```

Format that and get the flag:

```python
>>> bytearray.fromhex(format(m, 'x')).decode()
'picoCTF{m4yb3_Th0se_m3s54g3s_4r3_difurrent_5052620}'
```

The flag: `picoCTF{m4yb3_Th0se_m3s54g3s_4r3_difurrent_5052620}`