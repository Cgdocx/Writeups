# Wireshark twoo twooo two twoo...
Category: Forensics, 100 points

## Description

> Can you find the flag?

A network capture was attached.

## Solution

We get a network capture with mainly HTTP and DNS traffic:

```console
┌──(user@kali)-[/media/sf_CTFs/pico/Wireshark_twoo_twooo_two_twoo]
└─$ tshark -qz io,phs -r shark2.pcapng

===================================================================
Protocol Hierarchy Statistics
Filter:

eth                                      frames:4831 bytes:3355920
  ip                                     frames:4829 bytes:3355822
    tcp                                  frames:3276 bytes:3120750
      tls                                frames:71 bytes:115780
        tcp.segments                     frames:2 bytes:6576
      http                               frames:802 bytes:1879844
        tcp.segments                     frames:299 bytes:1605841
        mime_multipart                   frames:309 bytes:194144
          tcp.segments                   frames:309 bytes:194144
        data-text-lines                  frames:91 bytes:23987
          tcp.segments                   frames:90 bytes:23696
        xml                              frames:1 bytes:579
    udp                                  frames:1553 bytes:235072
      gquic                              frames:41 bytes:11668
      dns                                frames:1512 bytes:223404
  arp                                    frames:2 bytes:98
===================================================================
```

Scanning through the streams, we see this:

```console
┌──(user@kali)-[/media/sf_CTFs/pico/Wireshark_twoo_twooo_two_twoo]
└─$ tshark -r shark2.pcapng -qz follow,tcp,ascii,5

===================================================================
Follow: tcp,ascii
Filter: tcp.stream eq 5
Node 0: 192.168.38.104:63636
Node 1: 18.217.1.57:80
445
GET / HTTP/1.1
Host: 18.217.1.57
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.105 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9


        17
HTTP/1.0 200 OK

        235
Content-Type: text/html; charset=utf-8
Content-Length: 99
Server: Werkzeug/1.0.1 Python/3.6.9
Date: Mon, 10 Aug 2020 01:39:16 GMT

The official Red's Shrimp and Herring website is still under construction. Please check back later!
===================================================================
```

Did someone order a red herring?

```console
┌──(user@kali)-[/media/sf_CTFs/pico/Wireshark_twoo_twooo_two_twoo]
└─$ tshark -nr shark2.pcapng -Y 'frame contains "pico"' -T fields -e text
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]ba64526713f43758599aa98b}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]d2269bb63f68af5d98b98245}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]89c2ce559cf3c1ab6adbe34b}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]37c1257cd47389646da97810}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]406134331452d11ce73cd59e}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]3b021c1fe8663c0a422ce0d7}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]7e6b22fc856d380fb7573133}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]bdb05aac46fa0dd0045d2fc2}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]5e48cbccb44faa529946e249}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]6c9efcd12c03ac04429d884a}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]dcd59f1d957f511d822c8c06}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]8834af12aefd3c552cdc21b2}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]bdbeec8c6f81a6ad88fdc279}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]830700907b99741310acf08f}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]96210493a96fa25ac5e657aa}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]229b82196afbe0045ef025c4}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]6dc902e92062db4c2b3f455c}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]5d10bfd491f0390659162fb1}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]4a6003d1c47c5f0370984ab6}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]d2b05bd9aeee6e00dc4da5d7}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]45a06b1c68572390a2825d29}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]000502e878d1fb8022618923}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]9ce691077fe77e03a3c0939a}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]0cdbee415fb514b84aa58aea}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]cd378d8965c648233d79a252}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]8461f4097c685bcca637a6a9}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]5b9655486873ccc467f4bd6b}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]f14cc3a10142704b81f0fa07}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]bb1ccfcc2c2ebb91268dc944}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]f0dcc0817e150dd75e446838}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]ed785871ccb80473302a59db}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]9b2edb463cef030d34297bd0}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]29f9c90434383f277567992d}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]b8e31281eb0721597b6c6d62}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]1c70b1b7e1af2c7fb272d281}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]fa8693efdf26e1fd3cb44b4c}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]a856e3fbee6aac2fca0c20f3}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]089cdfe32509027b92485213}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]f06152781f780c201691713d}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]10e64664f7e36c7f5e0a4ef9}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]4f8b348c9c5b5d7bd7cb871b}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]b3dae61a8aba6187d671999a}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]260889afba1b0b6e7763aa31}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]2e00411365fb64c6a2f688ef}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]e64a39f3a4a0ee7bbd5d3ade}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]6d481eed16b5f77675030d7f}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]5294518ad3ed6748f853b0ab}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]3196062e669e3a2705f1a0d3}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]ec82c060778b54ffeb8fbd1f}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]2f5f4090fb774d960628e352}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]03d410b8163b685973937da7}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]4d2ddcf531a7865a997b00e5}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]fb2768410be593d3fe8c4bd4}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]5c526a00c942a8cebb6bb496}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]f555aaf5b6754f56e778e3fc}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]8cf41e1db45c070c94621c57}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]fcaad96a937b8c1f1134099b}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]98019a5c4ddaf9861b54efcb}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]a02466ddd207cfe7f785cb5c}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]386ab93be93a9b67e53b2fda}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]ac2f2c53023596cbf0dcbd0f}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]fb3fdaaa5143147678e9cbf9}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]ebf37ce46595edc46fba3f6d}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]0618c95f8cae14fedaa45b63}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]24bb5622839dda0c66755b14}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]939e6b46458b19082122d4bd}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]6ddfbf180ca366de0dec1b0c}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]9e068fd82dcbeb76131448bb}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]8b331318ce7adcbf5e70104d}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]e635de9d115087bc4f312bae}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]935850ac5c8140461e49d579}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]71f224b6dfcb6c8fce4f1601}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]6fd052140cbc64319b7ab0ac}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]89c6993346d278bff003c154}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]358373193522cc7f5628ed49}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]13c9f270804ef7826ecc298c}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]f3f9e4966dde22d4a84ca113}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]9ac2afb39300c7b66f2302c4}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]cc74e88d8facb2fd5e6ef34d}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]f5c2294fea5fb3bcd212e7f2}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]7f11759b4ad9f864efa7b5aa}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]445931319830e4e0ad2d2f09}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]0ce7e0407e001b453b05be22}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]209fc1ce9b2022ad1cd1b060}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]6370cbf8cdc1943c2e7aedf6}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]9a02c939cb4ff6ea189a140d}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]38446b78d1b2b334e0985447}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]07f422d9bd8656398f53433e}
Timestamps,HTTP/1.0 200 OK\r\n,\r\n,picoCTF{[AWS_SECRET_REMOVED]7a2883e57c8a44b34f35675c}
```

Obviously none of these flags worked. But what is all that DNS traffic?

```console
┌──(user@kali)-[/media/sf_CTFs/pico/Wireshark_twoo_twooo_two_twoo]
└─$ tshark -nr shark2.pcapng -Y 'dns' | head
  791   7.931626 192.168.38.104 → 8.8.8.8      DNS 93 Standard query 0x76aa A lDqoR16q.reddshrimpandherring.com
  792   7.943025      8.8.8.8 → 192.168.38.104 DNS 166 Standard query response 0x76aa No such name A lDqoR16q.reddshrimpandherring.com SOA a.gtld-servers.net
  793   7.947216 192.168.38.104 → 8.8.8.8      DNS 131 Standard query 0xcdd5 A lDqoR16q.reddshrimpandherring.com.us-west-1.ec2-utilities.amazonaws.com
  794   7.957680      8.8.8.8 → 192.168.38.104 DNS 203 Standard query response 0xcdd5 No such name A lDqoR16q.reddshrimpandherring.com.us-west-1.ec2-utilities.amazonaws.com SOA pdns1.ultradns.net
  795   7.958549 192.168.38.104 → 8.8.8.8      DNS 109 Standard query 0x5d2d A lDqoR16q.reddshrimpandherring.com.windomain.local
  796   7.967998      8.8.8.8 → 192.168.38.104 DNS 184 Standard query response 0x5d2d No such name A lDqoR16q.reddshrimpandherring.com.windomain.local SOA a.root-servers.net
  797   7.968981 192.168.38.104 → 8.8.8.8      DNS 93 Standard query 0xc847 A 1Th0dQuT.reddshrimpandherring.com
  798   8.049550      8.8.8.8 → 192.168.38.104 DNS 166 Standard query response 0xc847 No such name A 1Th0dQuT.reddshrimpandherring.com SOA a.gtld-servers.net
  799   8.050527 192.168.38.104 → 8.8.8.8      DNS 131 Standard query 0x21a5 A 1Th0dQuT.reddshrimpandherring.com.us-west-1.ec2-utilities.amazonaws.com
  800   8.061483      8.8.8.8 → 192.168.38.104 DNS 205 Standard query response 0x21a5 No such name A 1Th0dQuT.reddshrimpandherring.com.us-west-1.ec2-utilities.amazonaws.com SOA dns-external-master.amazon.com
```

We can see traffic to non-existent subdomains of `reddshrimpandherring.com`. Let's try to access the domain:

```console
┌──(user@kali)-[/media/sf_CTFs/pico/Wireshark_twoo_twooo_two_twoo]
└─$ curl http://www.reddshrimpandherring.com
Congrats! Was that tooooo easy?

Flag: [AWS_SECRET_REMOVED]X3liYm9iX2VsdHRpbH0=

┌──(user@kali)-[/media/sf_CTFs/pico/Wireshark_twoo_twooo_two_twoo]
└─$ echo [AWS_SECRET_REMOVED]X3liYm9iX2VsdHRpbH0= | base64 -d
picoCTF{f1shy_s1tu4ti0n_selbat_ybbob_elttil}  
```

That wasn't accepted either. The subdomains are a bit weird though. Might look like base64. If we extract them with some bash voodoo we get:

```console
┌──(user@kali)-[/media/sf_CTFs/pico/Wireshark_twoo_twooo_two_twoo]
└─$ tshark -nr shark2.pcapng -Y 'dns && ip.src == 192.168.38.104 && frame contains "local" && ip.dst==18.217.1.57' | awk '{ print $12 }' | awk -F. '{
print $1 }' | tr -d "\n"
[AWS_SECRET_REMOVED]fQ==fQ==
```

Decode that as base64:

```console
┌──(user@kali)-[/media/sf_CTFs/pico/Wireshark_twoo_twooo_two_twoo]
└─$ echo [AWS_SECRET_REMOVED]fQ==  | base64 -d
picoCTF{dns_3xf1l_ftw_deadbeef}
```

Finally, a flag that was accepted: `picoCTF{dns_3xf1l_ftw_deadbeef}`