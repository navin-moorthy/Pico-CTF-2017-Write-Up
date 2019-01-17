# PicoCTF_2017: Digital Camouflage

**Category:** Forensics
**Points:** 50
**Description:**

>We need to gain access to the school routers to cover our tracks. Let's try and see if we can find the password in the network data we captured earlier: [data.pcap](data.pcap)

**Hint:**

>It looks like an Administrator might have accessed the routers earlier. He had to have logged in with his password. Where would log in data be located in a network capture?

## Write-up
Looking up the `data.pcap` file in Wireshark lands us with packet #122

    
    HTML Form URL Encoded: application/x-www-form-urlencoded
    Form item: "userid" = "grassers"
        Key: userid
        Value: grassers
    Form item: "pswrd" = "cHJ2cUJaTnFZdw=="
        Key: pswrd
        Value: cHJ2cUJaTnFZdw==


Trying `cHJ2cUJaTnFZdw==` as the flag results in invalid... but wait, that looks like base64!

    $ echo "cHJ2cUJaTnFZdw==" | base64 -d
    8PFEo0ttHQ

Therefore, the flag is `8PFEo0ttHQ`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwNjgxMzkzNTZdfQ==
-->