# PicoCTF_2017: Missing Identity

**Category:** Master
**Points:** 100
**Description:**

>Turns out, some of the files back from Master Challenge 1 were corrupted. Restore this one [file](file) and find the flag.

**Hint:**

>What file is this?
What do you expect to find in the file structure?
All characters in the file are lower case or numberical. There will not be any zeros.

## Write-up

On first seeing the file with `cat`, we get some random letters but with the `strings` got something interesting.
```bash
....
....
....
0j@&
rguvt
UwjS
J(o*
"f)=
u$dq
>k0U
IEND
flag.pngPK
nottheflag1.pngPK
nottheflag2.pngPK
nottheflag3.pngPK
```
At the end of the provided output we can see 8 file names with additional **PK** letters appended to it. Quick google search shows us that PK are the initals of Phil Katz, co-creator of the ZIP file format and author of PKZIP. You can read more [here](http://www.garykessler.net/library/file_sigs.html).

As it's not that easy to just change the file name and extract from the archive, we will have to take a closer look at the structure of the file. I'm going to use  **Bliss**  hexeditor for Linux and  **hexdump**  for navigating the hexadecimal values.

```bash
w3ndige@W3ndige ~/Pobrane> hexdump -C file > texthexdump.txt
```

Now we can view the beginning (head) of the texthexdump.

```text
w3ndige@W3ndige ~/Pobrane> head texthexdump.txt
00000000  58 58 58 58 58 58 00 00  08 00 22 44 7f 4a b4 8b  |XXXXXX...."D.J..|
00000010  e4 67 2b 90 00 00 1c 90  00 00 08 00 00 00 66 6c  |.g+...........fl|
00000020  61 67 2e 70 6e 67 00 66  40 99 bf 89 50 4e 47 0d  |ag.png.f@...PNG.|
00000030  0a 1a 0a 00 00 00 0d 49  48 44 52 00 00 02 81 00  |.......IHDR.....|
00000040  00 00 3c 08 02 00 00 00  96 fa f7 6d 00 00 8f e3  |..<........m....|
00000050  49 44 41 54 78 9c ec fd  59 af 6c d9 96 1e 86 8d  |IDATx...Y.l.....|
00000060  31 9b d5 45 1f bb 3f 6d  b6 37 33 6f de ae ea 96  |1..E..?m.73o....|
00000070  cc 2a 56 d9 34 8b b2 20  1b 86 4c 91 80 21 d8 16  |.*V.4.. ..L..!..|
00000080  0c 43 06 fc e6 27 fd 0c  c2 80 fc e2 27 fa c1 10  |.C...'......'...|
00000090  6c 58 76 d1 86 21 c0 b2  45 53 02 2d 36 2e 56 cb  |lXv..!..ES.-6.V.|
```

We can clearly see the cause of the problem. Every file starts with the file signature, but this one has been overwritten with  **X**  characters. We can even view this error while trying to unzip this file.

```bash
w3ndige@W3ndige ~/Pobrane> unzip file
Archive:  file
file #1:  bad zipfile offset (local header sig):  0
  inflating: nottheflag1.png         
  inflating: nottheflag2.png         
  inflating: nottheflag3.png         
  inflating: nottheThis seems to be a zip file, [unzipped contents](unzipped) doesn't appear to contain flags, however we get an interesting error.

    Archive:  picoctf_2017_writeup/master/master_2/file
    file #1:  bad zipfile offset (local header sig):  0

Hmmm... Let's try to extract it with offsets

    $ dd if=file of=flag4.png         

Let's repair this with correct signature, which is **50 4B 03 04 00 00**. using a hex editor of your choice
```

We have everything for the last step -  **zip -F**  which will try to somehow repair the file.

```bash
w3ndige@W3ndige ~/Pobrane> zip -F file.zip --out new.zip
Fix archive (-F) - assume mostly intact archive
Zip entry offsets do not need adjusting
 copying: flag.png
	zip warning: Local Version Needed To Extract does not match CD: flag.png
 copying: nottheflag1.png
 copying: nottheflag2.png
 copying: nottheflag3.png
 copying: nottheflag4.png
```
![flag](unzipped/flag.png)

Thansk [w3ndige](https://www.rootnetsec.com/) for the writeupbs=1 count=93628 skip=43

Now we get a black PNG file? Hmm... Courtesy of @zst123, apparently [JD-GUI](http://jd.benow.ca/) happens to fix archives too! Doing a rename on `file` to `file.zip` and running it through JD-GUI gives us the flag! _Even though I'm sure that's cheating, heh!_

Therefore, the [flag](file.zip.src/flag.png) is `zippidydooda29494995`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTkxODQwMDgwNywtNzUxNzkzMTAsLTE3MD
Y5ODk3OTUsMTgyNDI1ODcyNiwtMTk3ODg2MTA2NCwtMTM0OTM1
MzU2Ml19
-->