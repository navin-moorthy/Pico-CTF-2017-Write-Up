# PicoCTF_2017: Little School Bus

**Category:** Forensics
**Points:** 75
**Description:**

>Can you help me find the data in this [littleschoolbus.bmp](littleschoolbus.bmp)?

**Hint:**

>Look at least significant bit encoding!!

## Write-up

Install [zsteg](https://github.com/zed-0xff/zsteg) and run the following command to get the hidden LSB details.
```
$ zsteg littleschoolbus.bmp 
imagedata           .. text: "~vusnljmkhifgXWWNNOVUV~}"
b1,lsb,bY           .. text: "flag{remember_kids_protect_your_headers_f5e8}"
b3,r,lsb,xY         .. file: very old 16-bit-int big-endian archive
b4,rgb,msb,xY       .. file: MPEG ADTS, layer I, v2, 112 kbps, 24 kHz, JntStereo
```
Well, that worked!
Therefore, the flag is `flag{remember_kids_protect_your_headers_5e31`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwOTEyODY4OTldfQ==
-->