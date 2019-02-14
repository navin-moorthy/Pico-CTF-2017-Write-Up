# PicoCTF_2017: SoRandom

**Category:** Cryptography
**Points:** 75
**Description:**

>We found [sorandom.py](sorandom.py) running at shell2017.picoctf.com:6399716768. It seems to be outputting the flag but randomizing all the characters first. Is there anyway to get back the original flag?

**Hint:**

>How random can computers be?

## Write-up
Firstly, it doesn't seem to be a random random.
    
    $ nc shell2017.picoctf.com 6399716768
    Unguessably Randomized Flag: BNZQ:3o8b2bgl0689u4aj640407963277k0fc449xg472190mwx6869b8pt10rwo92624

Looking in the `sorandom.py` code, we see that the random generator has already been preseeded!

    random.seed("random")

Okay, [let's crack our Python knuckles and get to work.](crack.py)
```
#! /usr/bin/env python
##
# Script for picoctf 2017 sorandom
# Script by NavinNavi
##
import socket
import random

# Open Socket
s = socket.socket();
s.connect(("shell2017.picoctf.com", 63997))

# Receive from Socket
instructions = s.recv(4096).decode("utf-8")
print(instructions)
encflag = ": ".join(instructions.split(":")[1:])
print(encflag)
flag = ""
# Random Seed
random.seed("random")

for c in encflag:
    if c.islower():
        flag += chr((ord(c) - ord('a') - random.randrange(0, 26)) % 26 + ord('a'))
        print(flag)
    elif c.isupper():
        flag += chr((ord(c) - ord('A') - random.randrange(0, 26)) % 26 + ord('A'))
        print(flag)
    elif c.isdigit():
        flag += chr((ord(c) - ord('0') - random.randrange(0, 10)) % 10 + ord('0'))
        print(flag)
    else:
        flag += c
        print(flag)

print(flag)
```
    $ ./crack.py
    FLAG: 0d6f1cac5615c7ae971761318430c9bb

Therefore, the flag is `107bd559693aef6692e1ed55ebe29514`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU2OTQwOTE2LDE5NjgxNTgxMzMsLTU3NT
A4NDM0NiwzNTEyMTcyOTJdfQ==
-->
