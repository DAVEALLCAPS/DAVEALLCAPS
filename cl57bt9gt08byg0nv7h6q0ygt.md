## Crack the hash Level 2 Write-up (Free Room on TryHackMe) Cracking Salted Hashes

%[https://youtu.be/NTRO6ZZGkWI]

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971892183/4wkQJswZh.png)


If you haven’t taken a look at [my level 1 write-up](https://medium.com/@daveallcaps/crack-the-hash-level-1-write-up-free-room-on-tryhackme-how-to-use-hashcat-on-linux-5d807e901ce5), I would recommend going back and reading it over because it explains our processes and the tools we are using. Level 2 of the **Crack the hash** room contains more hashes to crack, this time with salts and different hashing algorithms.  
If you don’t know what a salt is yet, don’t worry, it’s just an extra bit of data that is added to what we are hashing to make it harder to crack.

“In cryptography, a **salt** is random data that is used as an additional input to a one-way function that hashes data, a password or passphrase.”  
 — from Wikipedia page, [Salt (cryptography)](https://en.wikipedia.org/wiki/Salt_%28cryptography%29).

Let’s take a look at the tasks for level 2…

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971893623/VSuqajmSi.png)

### \[2–1\]

Hash: **F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85**  
Type: sha256

CrackStation:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971894831/DXF1RsFhw.png)

hashcat:

```
hashcat -m 1400 hash.txt rockyou.txt
```

Output:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971895829/x_wzo-eci.png)

Nice, we got the flag ‘paule’.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971896914/d5tMU4eex.png)

### \[2–2\]

Hash: **1DFECA0C002AE40B8619ECF94819CC1B**  
Type: NTLM

CrackStation:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971898066/JhPTMfCBL.png)

hashcat:

```
hashcat -m 1000 hash.txt rockyou.txt
```

Output:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971899168/UI4RsimuX.png)

Nice, we got the flag ‘n63umy8lkf4i’.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971900202/LJNsAV8mq.png)

### \[2–3\]

Hash: **$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.  
**Type: sha512crypt $6$  
Salt: aReallyHardSalt

CrackStation:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971901598/aH00z5aAT.png)

CrackStation isn’t capable of decoding salted hashes, luckily since we know the salt we can do this with hashcat. To do this I jumped over to my Windows machine that has a more efficient GPU for this kind of job.

hashcat command in PowerShell on Windows:  
(Linux users, just remove the “./” at the beginning of the statement)

```
PS C:\\Users\\dave\\Downloads\\hashcat-6.2.5> ./hashcat -m 1800 hash.txt rockyou.txt -O
```

Note the “-O” flag is a capital O and not a zero, this enables optimized kernels and enables use of my NVIDIA GPU.

Output:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971902901/9Fm1AgHc8.png)

Nice, we got the flag ‘waka99’.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971904065/GX6SJqZV1.png)

### \[2–4\]

Hash: **e5d8870e5bdd26602cab8dbe07a942c8669e56d6**  
Type: sha1  
Salt: tryhackme

CrackStation:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971905179/ccU8mgIyZ.png)

I spent some time trying to use hash-type=110 “sha1($pass.$salt)” & =120 “sha1($salt.$pass)” based on what I could see from the [example hash table](https://hashcat.net/wiki/doku.php?id=example_hashes), little did I know the hint held where I should have been looking.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971906298/-TqwMwkFi.png)

HMAC-SHA1 is NUM 160, so our command for hashcat is:

```
./hashcat -m 160 sha1saltedhash.txt rockyou.txt -O
```

But before we run it we need to format our hashfile by appending our salt to the end, separated with a colon. “hash**:salt”** As seen in this example screenshot:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971907392/V1wi9qKy8.png)

Screenshot from hashcat.net / example\_hashes

Once we add our salt to the end of the file we are ready to run our command.  
Since I am on my Windows machine I am gonna open it in notepad, add the :SALT, save, and start cracking.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971908487/oi6FembT8.png)

(HASH:SALT) Don’t forget to save.

Output:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971909968/BtvgOcHKQ.png)

Voila, the flag is ‘481616481616’.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971911045/ocvLwXvA1.png)

### Level 2 Conclusion

TryHackMe Hash Cracker Badge

Congrats, you made it to the end of this room with me! Today we learned how to crack some new hashing algorithms with hashcat, and also about salted hashes. I had fun with these write-ups, this was a good TryHackMe room overall to gain a base knowledge of hashing and cracking.

If you missed level 1 of this room yesterday, [you can find the write-up here](https://medium.com/@daveallcaps/crack-the-hash-level-1-write-up-free-room-on-tryhackme-how-to-use-hashcat-on-linux-5d807e901ce5).

### Until next time,   
**DAVE.**