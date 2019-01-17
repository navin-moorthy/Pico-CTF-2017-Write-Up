# PicoCTF_2017: ComputeAES

**Category:** Cryptography
**Points:** 50
**Description:**

>You found [this clue](clue.txt) laying around. Can you decrypt it?

**Hint:**

>Try online tools or python

## Write-up
Again using Python, I designed [a simple script](decrypt.py) using pycrypto.

**Python Script**

```
#! /usr/bin/env python3

##

# Script for PicoCTF computeAES challenge

# Created by Amos (LFlare) Ng

##

# Requires pycrypto

import base64

from Crypto.Cipher import AES

key = base64.b64decode("6v3TyEgjUcQRnWuIhjdTBA==")

ciphertext = base64.b64decode("rW4q3swEuIOEy8RTIp/DCMdNPtdYopSRXKSLYnX9NQe8z+LMsZ6Mx/x8pwGwofdZ")

crypter = AES.new(key, AES.MODE_ECB)

plaintext = crypter.decrypt(ciphertext).decode("utf-8")

print(plaintext)
```

    $ ./decrypt.py 
    flag{do_not_let_machines_win_983e8a2d}__________

Therefore, the flag is `flag{do_not_let_machines_win_983e8a2d`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTgzMzQ5NDg0M119
-->