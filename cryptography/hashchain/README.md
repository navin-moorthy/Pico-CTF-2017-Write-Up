# PicoCTF_2017: HashChain

**Category:** Cryptography
**Points:** 90
**Description:**

>We found a service hiding a flag! It seems to be using some kind of MD5 Hash Chain authentication to identify who is allowed to see the flag. Maybe there is a flaw you can exploit? [hcexample.py](hcexample.py) has some example code on how to calculate iterations of the MD5 hash chain. Connect to it at shell2017.picoctf.com:58801!

**Hint:**

>Connect from the shell with nc. Read up on how Hash Chains work and try to identify what could make this cryptosystem weak.

## Write-up
Another simple one, exploiting vulnerabilities of predictable seeds. In this case, believe it or not, the user ID is the seed! So, we create a python file that will use the user id and get the flag
```python
#!/usr/bin/env python2.7
import md5;
import sys;
import socket;

HOST = 'shell2017.picoctf.com';
PORT = 58801;

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM);
s.connect((HOST, PORT)); #Connect to server

s.recv(4096); #Receive the "Would you like to..." part
s.send('f\n'); #We would like to get flag
data = s.recv(4096); #Get data
print(data)
user = data.split('\n')[0].split(' ')[5]; #get user id
data = s.recv(4096);
print(data)
tkn = data.split('\n')[0]; #Get token

print("User: "+user+"\nToken: "+tkn);
seed = md5.new(user).hexdigest(); #Calculate seed
print("Seed: "+seed);
found = False;
hashc = seed; #Starting point of hashchain is the seed

while(found == False):
	if (md5.new(hashc).hexdigest() == tkn): #If the next hash is the token, we found the hash we are looking for
		print("Hash found: "+hashc);
		found = True;
	else:
		hashc = md5.new(hashc).hexdigest();
#s.recv(4096)
print("Sending hash...");
s.send(hashc+'\n'); #Send the hash
print(s.recv(4096)); #Get the flag
```
Now we run the python file to get the flag.
``

Therefore, the flag is `9494f4171092452602fa545ab927e99e`.

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTUwNzk2MzQ4Nl19
-->