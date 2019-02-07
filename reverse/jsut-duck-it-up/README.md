# PicoCTF_2017: JSut Duck It Up

**Category:** Reverse Engineering
**Points:** 100
**Description:**

>What in the world could this be?!?! [file](file)

**Hint:**

>Maybe start searching for uses of these chunks of characers? Is there anything on the Internet with them?

## Write-up
What in the world could this be?!?!

```text
[]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]]((!![]+[])[+!+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+([][[]]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+!+[]]+(+[![]]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+!+[]]]+(!![]+[])[!+[]+!+[]+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(![]+[])[+!+[]]+(+(!+[]+!+[]+[+!+[]]+[+!+[]]))[(!![]+[])[+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+
.
.
.
```

Wow, that's an really strange and huge file. Title of the challenge suggest that it may be connected with JavaScript, so let's do an quick google. After a little bit of fiddling, I've found this:  [JSFuck](http://www.jsfuck.com/). It is an esoteric subset of the JavaScript language that uses only six distinct characters in the source code. The characters are +, !, (, ), [, ].

Pasting this code into the text field, and running it produces an alert.

    aw_yiss_ducking_breadcrumbs_61637ee8

Therefore, the flag is `aw_yiss_ducking_breadcrumbs_61637ee8`.

Thanks [w3ndige](https://www.rootnetsec.com/)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTYwMDI2NDQzMywyMDg2MzEwNDY0XX0=
-->