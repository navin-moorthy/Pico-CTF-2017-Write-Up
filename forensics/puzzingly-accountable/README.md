# PicoCTF_2017: Puzzingly Accountable

**Category:** Forensics
**Points:** 100
**Description:**

>We need to find a password. It's likely that the updated passwords were sent over the network. Let's see if that's true: [data.pcap](data.pcap). Update 16:26 EST 1 Apr If you feel that you are close, make a private piazza post with what you have, and an admin will help out. The ones and sevens unfortunately look like each other.

**Hint:**

>How does an image end up on your computer? What type of packets are involved?

## Write-up
A challenge that tests whether you noticed a `/secret/*.png` GET request and then a manual individual TCP stream of each GET request to pngs.

We found the GETS that go to /secret/ in wireshark, yielding two results. For each of these we followed the TCP Stream (the wireshark tool), and saved the data as raw, then deleted everything up until the PNG header in a hex editor, saved as a PNG, and opened. We then proceeded to make a Piazza post because we couldn't tell the difference between the 1s and 7s in the final image.

![Final Image](https://github.com/NavinNavi19/Pico-CTF-2017-Write-Up/blob/master/forensics/puzzingly-accountable/FinalImage.jpg)

Therefore, the flag is `118e5d0aa18ce3d48bcf45b45a9e6fa8`
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwMjY1MTgxNzEsMjA1NDA2NTcxMV19
-->