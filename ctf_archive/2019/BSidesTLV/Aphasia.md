# Aphasia
* Category: Cryptography
* 500 Points
* Solved by the JCTF Team

## Description
> In this challenge, you will need to extract the flag from the secure records, but how?
> 
> http://memenc.challenges.bsidestlv.com/help

## Solution

We access the help API and get the following definitions:
```console
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/help
{
    "GET /{credID}":"Check existance and length of record at cred ID (all numbers in hex)", 
    "GET /read/{credID}:{len}":"Retrieve len bytes of non secure data from record at cred ID (all numbers in hex)", 
    "GET /hash/{credID}:{len}:{offset}":"Retrieve hash bytes of secure data from record at cred ID (all numbers in hex)", 
    "GET /help":"Show API help"
}
```

Let's try the APIs out.

We start with the "get details" API:
```console
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/0
{"header":"nonSecure","length":"14","data":""}
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/a
{"header":"nonSecure","length":"0b","data":""}
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/999
{"header":"Error","length":"","data":""}
```

Let's try to read from the first record:
```console
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/read/0:1
{"header":"nonSecure","length":"14","data":"8e"}
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/read/0:2
{"header":"nonSecure","length":"14","data":"8ec1"}
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/read/0:3
{"header":"nonSecure","length":"14","data":"8ec162"}
```

We can read from this record, marked as "Non-Secure", using this API.

What does the hash API give us?
We read the hash of 1 byte of data from record 0, offsets 0, 1, and 2.
```console
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/hash/0:1:0
{"header":"nonSecure","length":"20","data":"[AWS_SECRET_REMOVED]33ba9e73f2e40b474984a9c9"}
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/hash/0:1:1
{"header":"nonSecure","length":"20","data":"[AWS_SECRET_REMOVED]ee00e75a545147a27dadd982"}
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/hash/0:1:2
{"header":"nonSecure","length":"20","data":"[AWS_SECRET_REMOVED]c9c09357e107c99ba4d6948a"}
```

These are the SHA256 values for `8e`, `c1` and `62` respectively:
```console
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# echo -n "8e" | sha256sum
[AWS_SECRET_REMOVED]33ba9e73f2e40b474984a9c9  -
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# echo -n "c1" | sha256sum
[AWS_SECRET_REMOVED]ee00e75a545147a27dadd982  -
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# echo -n "62" | sha256sum
[AWS_SECRET_REMOVED]c9c09357e107c99ba4d6948a  -
```

So we can use the "hash" API to deduce the contents of a non-secure record, giving us what we read using the "read" API.

Now let's go find a secure record and try the same trick:

```console
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/12
{"header":"Secure","length":"05","data":""}
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/read/12:0
{"header":"Secure","length":"05","data":"Secure data, use hash interface"}
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# curl http://memenc.challenges.bsidestlv.com/hash/12:1:0
{"header":"Secure","length":"20","data":"[AWS_SECRET_REMOVED]ad893c4ed271d3622ac14548"}
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# for i in `seq 0 256`; do chr=$(printf "%02x" $i); echo -n $chr | sha256sum &
& echo $chr; done | grep [AWS_SECRET_REMOVED]ad893c4ed271d3622ac14548 -A1
[AWS_SECRET_REMOVED]ad893c4ed271d3622ac14548  -
f7
```

Looks like this works for secure record as well.

Using the following script, we can extract all the secure records:
```python
import requests
import hashlib
import string

URL = "http://memenc.challenges.bsidestlv.com"

def get_details(record_id):
    r = requests.get("{}/{:x}".format(URL, record_id))
    j = r.json()
    if j["header"] == "Error":
        return None
    return j

def get_data(record_id, length):
    r = requests.get("{}/read/{:x}:{:x}".format(URL, record_id, length))
    j = r.json()
    if j["header"] == "Error":
        return None
    return j

def get_hash(record_id, length, offset):
    r = requests.get("{}/hash/{:x}:{:x}:{:x}".format(URL, record_id, length, offset))
    j = r.json()
    if j["header"] == "Error":
        return None
    return j


sha256_map = {}
for c in range(256):
    hex_repr = '{:02x}'.format(c)
    sha256_map[hashlib.sha256(hex_repr.encode()).hexdigest()] = hex_repr

record_id = -1
while True:
    record_id += 1
    details = get_details(record_id)

    if details is None:
        break

    if details["header"] == "nonSecure":
        continue

    msg = ""
    offset = 0
    while True:
        res = get_hash(record_id, 1, offset)
        if res is None:
            print ("Record #{}: '{}'".format(record_id, msg))
            break

        msg += sha256_map[res["data"]]
        offset += 1
```

Running it, we get:
```console
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# python3 extract.py
Record #2: '[AWS_SECRET_REMOVED]756e217d'
Record #12: '[AWS_SECRET_REMOVED]756e217d'
Record #16: '[AWS_SECRET_REMOVED]0e4c88'
Record #17: '[AWS_SECRET_REMOVED]756e217d'
Record #18: 'f763f936ad'
Record #21: '[AWS_SECRET_REMOVED]756e217d'
Record #28: '[AWS_SECRET_REMOVED]756e217d'
Record #29: '[AWS_SECRET_REMOVED]756e217d'
Record #31: '[AWS_SECRET_REMOVED]756e217d'
Record #36: '[AWS_SECRET_REMOVED]756e217d'
Record #47: '[AWS_SECRET_REMOVED]756e217d'
Record #49: '[AWS_SECRET_REMOVED]756e217d'
Record #56: '[AWS_SECRET_REMOVED]756e217d'
Record #57: '[AWS_SECRET_REMOVED]756e217d'
Record #66: '[AWS_SECRET_REMOVED]756e217d'
Record #67: '2c1f56e75c38ce06'
Record #74: '[AWS_SECRET_REMOVED]756e217d'
Record #77: 'd1'
Record #78: '[AWS_SECRET_REMOVED]0dfeddac6709ff0a'
Record #83: '[AWS_SECRET_REMOVED]756e217d'
Record #85: '[AWS_SECRET_REMOVED]756e217d'
Record #86: '[AWS_SECRET_REMOVED]756e217d'
Record #87: '[AWS_SECRET_REMOVED]756e217d'
Record #92: '[AWS_SECRET_REMOVED]756e217d'
Record #93: '[AWS_SECRET_REMOVED]756e217d'
Record #100: 'd474bb75f4e6ae882a0bb15ecdfd'
Record #101: '[AWS_SECRET_REMOVED]756e217d'
Record #102: '[AWS_SECRET_REMOVED]bb'
Record #115: '[AWS_SECRET_REMOVED]de1d'
Record #124: '[AWS_SECRET_REMOVED]756e217d'
Record #125: '[AWS_SECRET_REMOVED]756e217d'
```

One value seems to repeat itself many times, what does it represent?
```console
root@kali:/media/sf_CTFs/bsidestlv/Aphasia# echo [AWS_SECRET_REMOVED]756e217d | xxd -p -r
BSidesTLV{This was fun!}
```