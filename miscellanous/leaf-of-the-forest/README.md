# PicoCTF_2017: Leaf of the Forest

**Category:** Miscellanous
**Points:** 30
**Description:**

>We found an even bigger directory tree hiding a flag starting at /problems/db39b5c002d8445dc6d2bbf49a8ccc37. It would be impossible to find the file named flag manually...

**Hint:**

>Is there a search function in Linux? Like if I wanted to 'find' something...

## Write-up
Well, apparently the solution for the previous [challenge](../leaf-of-the-tree/) is used for this one. So, lets `find` the flag!

    $cd /problems/db39b5c002d8445dc6d2bbf49a8ccc37
    
    $ find -type f -name flag -exec cat {} \;
	c99501b0fe95402ed1c9191102fe1b68

Easy!
Therefore, the flag is `7ffb59b2f309c09959ba333d0af88565`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwMjg3MzQ0NSwtMTg5NTYwMDA0M119
-->