# PicoCTF_2017: Looooong

**Category:** Miscellanous
**Points:** 20
**Description:**

>I heard you have some "delusions of grandeur" about your typing speed. How fast can you go at shell2017.picoctf.com:44840?

**Hint:**

>Use the nc command to connect!
I hear python is a good means (among many) to generate the needed input.
It might help to have multiple windows open

## Write-up
With an easily designed Python script, the flag prints out easily.

  **Python Script**

```
#! /usr/bin/env python3
##
# Script for PicoCTF Looooong challenge
# Created by NavinNavi
##
import re
import socket

# Open socket
s = socket.socket()
s.connect(("shell2017.picoctf.com", 44840))

# Receive initial instructions
instructions = s.recv(4096).decode("utf-8")
print(instructions)

# Parse instructions
letter = re.search("'([A-Za-z])' character", instructions).group(1)
count = int(re.search("'([0-9]+)' times", instructions).group(1))
end = re.search("followed by a single '([a-zA-Z0-9])'", instructions).group(1)

# Parse reply
reply = (letter * count) + end + "\n"

# Send reply
s.send(reply.encode("utf-8"))

# Receive reply to reply
print(s.recv(4096).decode("utf-8"))
```
Now run the script to get the flag
    
  
    $ ./script.py
	To prove your skills, you must pass this test.
	Please give me the 's' character '705' times, followed by a single '4'.
	To make things interesting, you have 30 seconds.
	Input:

	You got it! You're super quick!
	Flag: with_some_recognition_and_training_delusions_become_glimpses_97b1f7514342008e75bd0238aa007d09

Therefore, the flag is `with_some_recognition_and_training_delusions_become_glimpses_97b1f7514342008e75bd0238aa007d09`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMzM3MDkzNTk3LDM4MzEzNDU1MV19
-->