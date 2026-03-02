# Super Safe RSA
Cryptography, 350 points

## Description:
> Dr. Xernon made the mistake of rolling his own crypto.. Can you find the bug and decrypt the message? 



## Solution:

After connecting to the server, we receive a response similar to the the following:
```console
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA# nc 2018shell3.picoctf.com 1317
c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]
n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]
e: 65537
```

The n value seems pretty small, and it turns out we can factorize it using a service such as [FactorDB](http://factordb.com/).

FactorDB might take a while when attempting to factorize new numbers, so we need to poll the service after the initial request.

When we receive the result (p and q), we can easily decrypt the message.

The script:
```python
import gmpy2
from factordb.factordb import FactorDB
from pwn import *
import time
import requests

r = remote("2018shell3.picoctf.com", 1317)
output = r.recvall()
params = {}
for line in output.rstrip().split("\n"):
    param, value = line.split(": ")
    log.info("{}: {}".format(param, value))
    params[param] = int(value.rstrip())

log.info("Connecting to FactorDB")

# It looks like in some cases, the FactorDB won't factorize a number unless
# we first access the web API.
requests.get("http://factordb.com/index.php?query={}".format(params["n"]))

status = ''
while status != 'FF':
    f = FactorDB(params["n"])
    f.connect()
    factor_list = f.get_factor_list()
    status = f.get_status()
    log.info("Received from FactorDB: {} (status: {})".format(factor_list, status))
    time.sleep(15)

p, q = factor_list
log.info("p: {}".format(p))
log.info("q: {}".format(q))
assert(p * q == params["n"])

ph = (p-1)*(q-1)
log.info("Phi: {}".format(ph))
d = gmpy2.invert(params["e"], ph)
log.info("d: {}".format(d))

plaintext = pow(params["c"], d, params["n"])
log.success("Flag: {}".format(format(plaintext, 'x').decode("hex")))
```

The output:
```console
root@kali:/media/sf_CTFs/pico/Super_Safe_RSA# python solve.py
[+] Opening connection to 2018shell3.picoctf.com on port 1317: Done
[+] Receiving all data: Done (177B)
[*] Closed connection to 2018shell3.picoctf.com port 1317
[*] c: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]
[*] n: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]
[*] e: 65537
[*] Connecting to FactorDB
[*] Received from FactorDB: [[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]L] (status: C)
[*] Received from FactorDB: [[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]L] (status: C)
[*] Received from FactorDB: [[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]L] (status: C)
[*] Received from FactorDB: [[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]L] (status: C)
[*] Received from FactorDB: [[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]L] (status: C)
[*] Received from FactorDB: [[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]L] (status: C)
[*] Received from FactorDB: [[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]L] (status: C)
[*] Received from FactorDB: [[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]L] (status: C)
[*] Received from FactorDB: [93623660453440872539167520505354536801L, [AWS_SECRET_REMOVED]67L] (status: FF)
[*] p: 93623660453440872539167520505354536801
[*] q: [AWS_SECRET_REMOVED]67
[*] Phi: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]
[*] d: [AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]
[+] Flag: picoCTF{us3_l@rg3r_pr1m3$_7542}
```

The flag: picoCTF{us3_l@rg3r_pr1m3$_7542}