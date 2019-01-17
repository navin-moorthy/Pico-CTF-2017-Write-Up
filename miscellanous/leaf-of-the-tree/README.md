# PicoCTF_2017: Leaf of the Tree

**Category:** Miscellanous
**Points:** 20
**Description:**

>We found this annoyingly named directory tree starting at /problems/a45d1519bd193bc3a273744c83fad1e2. It would be pretty lame to type out all of those directory names but maybe there is something in there worth finding? And maybe we dont need to type out all those names...? Follow the trunk, using cat and ls!

**Hint:**

>Tab completion is a wonderful, wonderful thing

## Write-up
I am a big fan of not wasting time by tab completion, thus we are going to cheat and use `find`.

    $ cd /problems/a45d1519bd193bc3a273744c83fad1e2
    
    $ find -type f
	./trunk/trunk9ef5/trunkded5/branch1e76/leaf69bd
	./trunk/trunk9ef5/trunkded5/trunk3f6a/trunk6034/trunk41fe/branchb7ed/leaf63c2
	./trunk/trunk9ef5/trunkded5/trunk3f6a/trunk6034/trunk41fe/branchb7ed/leaf1ecc
	./trunk/trunk9ef5/trunkded5/trunk3f6a/trunk6034/trunk41fe/trunkb847/trunk7d34/flag
	./trunk/trunk9ef5/trunkded5/trunk3f6a/trunk6034/trunk41fe/trunkb847/trunk7d34/branche7e8/leafbd67
	./trunk/trunk9ef5/trunkded5/trunk3f6a/trunk6034/trunk41fe/trunkb847/trunk7d34/branche7e8/leafd627
	./trunk/trunk9ef5/trunkded5/trunk3f6a/trunk6034/branchb8fc/leaf723b
	./trunk/trunk9ef5/trunkded5/trunk3f6a/branch8807/leaffcd3
	./trunk/trunk9ef5/trunkded5/trunk3f6a/branch8807/leaf2352
	./trunk/branchebba/leaff8e4
	./trunk/branchebba/leafd038
	./trunk/branchebba/leaff61d
Hmm, that flag file looks interesting.

    $ cat ./trunk/trunk9ef5/trunkded5/trunk3f6a/trunk6034/trunk41fe/trunkb847/trunk7d34/flag
	1510e551a2821bd027da10a7653814c8

Therefore, the flag is `5e3d48f32a6d6e17a8102d3cbae36283`.  
<!--stackedit_data:
eyJoaXN0b3J5IjpbODc2MTQ1NDg4LDE0MTg0Mzg0ODVdfQ==
-->