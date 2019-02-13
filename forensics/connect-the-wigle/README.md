# PicoCTF_2017: Connect The Wigle

**Category:** Forensics
**Points:** 140
**Description:**

>Identify the data contained within [wigle](wigle) and determine how to visualize it. Update 16:26 EST 1 Apr If you feel that you are close, make a private piazza post with what you have, and an admin will help out.

**Hint:**

>Perhaps they've been storing data in a database. How do we access the information?
How can we visualize this data? Maybe we just need to take a step back to get the big picture?
Try zero in the first word of the flag, if you think it's an O.
If you think you're super close, make a private piazza post with what you think it is.

## Write-up

Identify the data contained within wigle and determine how to visualize it.

This time we get another sqlite file called  **wigle**.

```bash
w3ndige@W3ndige ~/Pobrane> file wigle
wigle: SQLite 3.x database, last written using SQLite version 3008007
```

Now we can open it up, and try to determine, whichThe solution to this challenge was very simple, except that if you are an over-thinker and over-analyzer like me, you tend to miss out ofn the tables in a database should be our target.

```sql
sqlite> SELECT * FROM sqlite_master WHERE type='table';
table|android_metadata|android_metadata|2|CREATE TABLE android_metadata (locale text)
table|location|location|3|CREATE TABLE location (_id int, bssid text, level int, lat double, lon double, altitude double, accuracy float, time long)
table|network|network|4|CREATE TABLE network (bssid text, ssid text, frequency int, capabilities text, lasttime long, lastlat double, lastlon double, type text)
```

Great, location table, with some longitude, and latitude columns. Let's view them.

```sql
sqlite> select lat, lon from location;
-30.0|94.04
-29.99|94.04
-29.98|94.04
-29.98|94.045
-29.98|94.05
-29.97|94.04
-29.96|94.04
-29.96|94.05
-29.96|94.06
-30.0|94.14
-29.99|94.14
.
.
.
```

Mysterious list of long coordinates. We can try to plot them using  [hamstermap](http://www.hamstermap.com/quickmap.php)  website. But firstly, we will have to change all the  **|**  charactereasiest step.

Firstly, analysing the file tells us that it's an SQLite file, so let's open it up and view the data. Next, dumping the `lat` and `lon` to a CSV file and plotting it on a map... wait for it... gives us

![flag](flag.png)

My original attempt was foolish, trying to deduce any erroneous locations into the  **,**. Then we're ready to plot them on a map.

![Flag](https://github.com/NavinNavi19/Pico-CTF-2017-Write-Up/blob/master/forensics/connect-the-wigle/flag.PNG)

After taking a closer look, we can see that the flag is FLAG{F0UND_M305148BBEA}.

Thanks [w3ndige](https://www.rootnetsec.com/picoctf-connect-the-wiggle/) for the cleat writeupdata and ploting that instead.

Therefore, the flag is `flag{f0und_m3_ee263b5f}`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjAyNTk1NDc2Niw1OTc4ODEwMDQsLTE1Nz
M2NDYyNzMsLTE3MjA2OTczMl19
-->