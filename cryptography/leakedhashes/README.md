# PicoCTF_2017: LeakedHashes

**Category:** Cryptography
**Points:** 90
**Description:**

>Someone got hacked! Check out some service's password hashes that were leaked at hashdump.txt! Do you think they chose strong passwords? We should check... The service is running at shell2017.picoctf.com:44804!

**Hint:**

>See if you can crack any of the login credentials and then connect to the service as one of the users. What's the chance these hashes have actually already been broken by someone else? Are there websites that host those cracked hashes? Connect from the shell with nc.

## Write-up
Let's try to see what the login looks like.

    $ nc shell2017.picoctf.com 44804
    enter username:
    root
    root's password:

Well, damn, we only have root's hash. Let's do a simple `cat hashdump.txt | cut -d ':' -f 2` to retreive all password hashes and go to HashKiller website to get the answer for the hashes.
```
be3f7de032d2e398ec542a7df71e0417 [Not found]
a14ca920b8a9c1ee742b4248e7f9f56c MD5 : r3ckr3ck
2cb699789d11d94fee780859adfabb91 MD5 : sch4h
08a81fafe787c93d02eb338b75f61819 MD5 : d3r7h
e2c1a2a69ebfa9e937b4f375c0948b40 MD5 : p4muncl3
b6eae1ba4ea6610cb0d12f0c21e1d2ec MD5 : w1l3w00ld
7b955cc1c21559338fdaf747434f3b36 MD5 : r1gh7
2cb699789d11d94fee780859adfabb91 MD5 : sch4h
74fe6370a9626ef1887c0e3ca4e6b600 MD5 : m4s7m3g4
510251930c07bbcbeb1908e2c22d8d66 MD5 : c4s4s00y
8b1660cfc5ce8217cb9188cc6b652e91 MD5 : p4p3r
ee4d5bc25a2771d95fdbd24452e355ad MD5 : r3s4w
189dbb843e5420471ba9f10f03d8e9dc MD5 : m0n3y
c2463a810b82134c6844a1ae1bcd1625 MD5 : g3mg4s7
dd7039976c7c7e6178868983ad026a1d MD5 : 734rs74nd
21b6a74930ef75e32f69bc0b49fbe8ff MD5 : hum4n
f3ab9dc32a394eb1cdf922659abcdead MD5 : 0u70u73r
3a663354e45f1dbf9702ffa5dc799c2f MD5 : 3l3c7
9472604ded1bcfac3b006331195a3163 MD5 : r4cyl4k3r
0710f636e10edae58aea9a30125239ae MD5 : m374l
435fc037deebaf8d9e3c4a4fb3e2fac7 MD5 : k3mpk3rs3
a64bb7fd1868a7bac23a384d014d352d MD5 : fr33d
3a663354e45f1dbf9702ffa5dc799c2f MD5 : 3l3c7
08a81fafe787c93d02eb338b75f61819 MD5 : d3r7h
89cfc208ee100d1d4895d4bf3ca6310c MD5 : m47ch
f8d4a51d618d84e02e2914901b6e59c8 MD5 : n377y
91f7fceef0044b405d862d132fa667e9 MD5 : dry3x3r7
6cf6041fc2df249eaf313a269c5bd200 MD5 : s4l73p1c
a28f30dd072b0aa66337f37526e7efa0 MD5 : chumch0r3
ca63eab394b98edb1f1990b5fe94c0b7 MD5 : l1b3l
6a8cd19a61e34f8ca4e0e7a14bb0e45b MD5 : s4ppy
66bf6b80df55195cfa25d48ce38f2b60 MD5 : subsucr3
f46fbb02514e281be7c5f49581a7c3e5 MD5 : l1b3r
6a8cd19a61e34f8ca4e0e7a14bb0e45b MD5 : s4ppy
f8d4a51d618d84e02e2914901b6e59c8 MD5 : n377y
cc5847ba595f097885b2bbe5eb940e22 MD5 : 43ry43g1s
8bb421ff32a77382408a6e1539855e40 MD5 : r4m13
189dbb843e5420471ba9f10f03d8e9dc MD5 : m0n3y
89689941d40794e311ef8bc7061b9944 MD5 : 7h1ck
9472604ded1bcfac3b006331195a3163 MD5 : r4cyl4k3r
cc5847ba595f097885b2bbe5eb940e22 MD5 : 43ry43g1s
cee42b4df8a585981a591220ece71c65 MD5 : r0br0ck
8b1660cfc5ce8217cb9188cc6b652e91 MD5 : p4p3r
a64bb7fd1868a7bac23a384d014d352d MD5 : fr33d
3a663354e45f1dbf9702ffa5dc799c2f MD5 : 3l3c7
699c0be567e2cbc16214dc4bc531cf4d MD5 : lymph
cead6186a16bf5486e5314ed8352d91e MD5 : 4ss37
7a4a8a79bdc17144c3b10226e1b92d60 MD5 : p3r1l
ac2f556a0eb415745b31e14a91d55d75 MD5 : muc1d
8b1660cfc5ce8217cb9188cc6b652e91 MD5 : p4p3r
3f4e535a671209f394817559372286f2 MD5 : d3b17
```

 We are unable to find out what's root's password but never mind that, let's try `christene`.
```
 $ nc shell2017.picoctf.com 44804
enter username:
christene
christene's password:r3ckr3ck
welcome to shady file server. would you like to access the cat ascii art database? y/n
y

     /\_/\ 
    ( o o )
  G-==_Y_==-M
      `-'
      
  /\ /\ 
  (O o)
=(: ^ :)=  
  '*v*'
  
 |\_/|     
 (. .)
  =w= (\   
 / ^ \//   
(|| ||)
,""_""_ .

     /\_/\ 
    ( o o )
   -==_Y_==- 
      `-'
    /\**/\ 
   ( o_o  )_)
   ,(u  u  ,),
  {}{}{}{}{}{}
  
  /\_/\ 
 ( o.o )
  > ^ <
  
       /\_/\ 
  /\  / o o \ 
 //\ \~(*)~/
 `  \/   ^ /
    | \|| ||  
    \ '|| ||  
     \)()-())
     
   A_A
  (-.-)
   |-|   
  /   \  
 |     |  __
 |  || | |  \___
 \_||_/_/
 
     /\__/\ 
    /`    '\ 
  === 0  0 ===
    \  --  /    - flag is 3977df525282eaf0e99f86efd2b645ed

   /        \ 
  /          \ 
 |            |
  \  ||  ||  /
   \_oo__oo_/#######o
   
  /\___/\ 
 ( o   o )
 (  =^=  ) 
 (        )
 (         )
 (          )))))))))))
 
 /\ /\ 
 (O o)
=(:^:)=  
   U
   
    _,,/|
    \o o' 
    =_~_=
    /   \ (\ 
   (////_)//
   ~~~
   
   /\     /\ 
  {  `---'  }
  {  O   O  }  
~~|~   V   ~|~~  
   \  \|/  /   
    `-----'__
    /     \  `^\_
   {       }\ |\_\_   W
   |  \_/  |/ /  \_\_( )
    \__/  /(_E     \__/
      (  /
       MM
       
              ("`-''-/").___..--''"`-._
               `6_ 6  )   `-.  (     ).`-.__.`)
               (_Y_.)'  ._   )  `._ `. ``-..-'
             _..`--'_..-_/  /--'_.' ,'
           (il),-''  (li),'  ((!.-'
           
from http://user.xmission.com/~emailbox/ascii_cats.htm
```
Easy.
Therefore, the flag is `3977df525282eaf0e99f86efd2b645ed`
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTcyNTgwNzQxOV19
-->