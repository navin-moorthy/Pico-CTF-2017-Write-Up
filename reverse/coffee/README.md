# PicoCTF_2017: Coffee

**Category:** Reverse Engineering
**Points:** 115
**Description:**

>You found a suspicious USB drive in a jar of pickles. It contains this file file.

**Hint:**

>Is there a way to get the source of the program?

## Write-up
Using something like JD-GUI, we get the source of the class file
```java
import  java.util.Base64.Decoder;

  

public  class  problem {

public  problem() {}

public  static  String  get_flag() { String  str1  =  "Hint: Don't worry about the schematics";

String  str2  =  "eux_Z]\\ayiqlog`s^hvnmwr[cpftbkjd";

String  str3  =  "Zf91XhR7fa=ZVH2H=QlbvdHJx5omN2xc";

byte[] arrayOfByte1  =  str2.getBytes();

byte[] arrayOfByte2  =  str3.getBytes();

byte[] arrayOfByte3  =  new  byte[arrayOfByte2.length];

for (int  i  =  0; i <  arrayOfByte2.length; i++)

{

arrayOfByte3[i] = arrayOfByte2[(arrayOfByte1[i] -  90)];

}

System.out.println(java.util.Arrays.toString(java.util.Base64.getDecoder().decode(arrayOfByte3)));

return  new  String(java.util.Base64.getDecoder().decode(arrayOfByte3));

}

public  static  void  main(String[] paramArrayOfString) {

System.out.println("Nothing to see here");

}

}
```
A python alternative to the java program has been written to solve the program.
```python
#!/usr/bin/env python2.7

import base64

a = "eux_Z]\\ayiqlog`s^hvnmwr[cpftbkjd"
b = "Zf91XhR7fa=ZVH2H=QlbvdHJx5omN2xc"
cd = []

for x in range(len(b)):
    c = b[(ord((a)[x])-90)]
    cd.append(c)

dc = ''.join(cd)

d = base64.b64decode(dc)

print d

```
```text
$ python solve.py 
flag_{pretty_cool_huh}

Therefore, the flag is `flag_{pretty_cool_huh}`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU5ODEyMjE1NiwtMTc4NDkwNzQzMl19
-->