# ∑BIT
Category: Misc., 100 points

## Description

> We hid an email in this challenge, find it to get the flag (and feel free to send your CV to this email 😉).
> 
> Note: the flag is cstechnion{sha1(<email>)}

A binary file was attached.

## Solution

Let's check the attached file:

```console
┌──(user@kali)-[/media/sf_CTFs/technion/BIT]
└─$ file ./sigma_ctf
./sigma_ctf: ASCII text, with very long lines

┌──(user@kali)-[/media/sf_CTFs/technion/BIT]
└─$ head ./sigma_ctf
[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]h+V/Uf4DkjcPHwAeAAA=
```

Looks like base64, let's decode:

```console
┌──(user@kali)-[/media/sf_CTFs/technion/BIT]
└─$ base64 -d sigma_ctf > phase1

┌──(user@kali)-[/media/sf_CTFs/technion/BIT]
└─$ file phase1
phase1: gzip compressed data, last modified: Sun Mar 31 15:30:16 2019, from Unix, original size modulo 2^32 7680
```

Now it's GZip, let's extract:

```console
┌──(user@kali)-[/media/sf_CTFs/technion/BIT]
└─$ mv phase1 phase1.gz

┌──(user@kali)-[/media/sf_CTFs/technion/BIT]
└─$ gunzip -v phase1.gz
phase1.gz:       65.7% -- replaced with phase1

┌──(user@kali)-[/media/sf_CTFs/technion/BIT]
└─$ file phase1
phase1: POSIX tar archive

┌──(user@kali)-[/media/sf_CTFs/technion/BIT]
└─$ tar -xvf phase1
bin.elf
```

Finally, we got a binary:

```console
┌──(user@kali)-[/media/sf_CTFs/technion/BIT]
└─$ file bin.elf
bin.elf: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.3, for GNU/Linux 2.6.26, BuildID[sha1]=[AWS_SECRET_REMOVED], not stripped
```

However it's compiled for ARM, so might not be streightforward to run. Let's start by inspecting the decompilation with Ghidra:

```c
undefined4 main(void)

{
  size_t sVar1;
  undefined4 uVar2;
  
  __isoc99_scanf(&DAT_00008638,input);
  sVar1 = strlen(input);
  if (sVar1 == 20) {
    if ((input._0_4_ ^ 0x12345678) == xor_result) {
      if (input._4_4_ + input._0_4_ == -0x1e2f342c) {
        if (input._8_4_ - input._4_4_ == -0xd28eff9) {
          if (input._8_4_ - input._12_4_ == -0x528ef05) {
            if (input._16_4_ + input._12_4_ == -0x26273b65) {
              printf("Correct! The flag is: %s\n",input);
              uVar2 = 0;
            }
            else {
              uVar2 = 1;
            }
          }
          else {
            uVar2 = 1;
          }
        }
        else {
          uVar2 = 1;
        }
      }
      else {
        uVar2 = 1;
      }
    }
    else {
      uVar2 = 1;
    }
  }
  else {
    uVar2 = 1;
  }
  return uVar2;
}
```

Seems easy enough to reverse:

```c
#include <stdio.h>

int main()
{
    int a[6] = {0};
    a[0] = 0x12345678 ^ 0x7F533F0B;
    a[1] = -0x1e2f342c - a[0];
    a[2] = -0xd28eff9 + a[1];
    a[3] = a[2] + 0x528ef05;
    a[4] = -0x26273b65 - a[3];
    printf("%s", (char*)&a);

    return 0;
}
```

Output:

```console
┌──(user@kali)-[/media/sf_CTFs/technion/BIT]
└─$ gcc solve.c -o solve && ./solve
sigmabithr@gmail.com
```

So the flag is:

```console
┌──(user@kali)-[/media/sf_CTFs/technion/BIT]
└─$ echo -n "sigmabithr@gmail.com" | sha1sum | awk '{ printf "cstechnion{%s}", $1; }'
cstechnion{[AWS_SECRET_REMOVED]}
```