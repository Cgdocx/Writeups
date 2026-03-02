# Relatively Secure Algorithm
Category: Cryptography

## Description
> The department of EEvil is plotting something, we found this file on one of the faculty's computers, can you figure out what it is?

A text file was attached.

## Solution

From the name of the challenge we can guess that it will deal with RSA.

Let's check the attached file:

```console
root@kali:/media/sf_CTFs/technion/Relatively_Secure_Algorithm# cat message.txt
n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]884299695000130785666275868697293
e: 65537
p: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]09214835974060763
q: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]97882366306600311
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]323701265358726624318912749452796
```

This looks like textbook RSA where we even know the private key material.

It can easily be decoded with the following script:

```python
import gmpy2

with open("message.txt") as f:
    params = {}
    for line in f:
        name, value = line.strip().split(": ")
        params[name] = int(value)
        
    assert(params["p"] * params["q"] == params["n"])
    ph = (params["p"] - 1) * (params["q"] - 1)
    d = gmpy2.invert(params["e"], ph)
    plaintext = pow(params["c"], d, params["n"])
    print(bytes.fromhex(format(plaintext, 'x')).decode("ascii"))
```

Output:
```console
root@kali:/media/sf_CTFs/technion/Relatively_Secure_Algorithm# python3 solve.py
My dear friends of the departement of EEvil
Our sworn enemy, vaad madmach, is planning to organize a CTF competition for the student!
This is outragous! we can't let them do something like that while we organize nothing!
So I, and my colleagues at the vaad of EEvil are planning to steal all their flags and post them before the competition!
We already found this little flag on one of their computers: cstechnion{Rs4_1s_34sy_4Nd_7hIS_Is_m374}
We will publish more flags as we find them

Long live the EEmpire!
```