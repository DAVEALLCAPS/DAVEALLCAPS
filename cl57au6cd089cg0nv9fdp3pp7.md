## Crack the hash Level 1 Write-up (Free Room on TryHackMe) How to Use Hashcat on Linux

Crack the hash is a free room on TryHackMe, found [here](https://tryhackme.com/room/crackthehash). It is rated Easy and contains a series of hashes to be cracked. At first glance I see some hash formats that I instantly recognize, like md5 at level 1 task 1, and others that are much longer or have formats that I haven’t worked with before.

%[https://youtu.be/NTRO6ZZGkWI]

My plan is to start by using [CrackStation.net](https://crackstation.net/), while also testing against hashcat to demonstrate full understanding. I am using Ubuntu 20.04, so firstly I’m going to install hashcat.

```
sudo apt install hashcat
```

And grab the rockyou.txt password list using wget.

```
wget https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt
```

Let’s take a look at the tasks…

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971915504/M6JMEJFUA.png)

Pasting the first hash into CrackStation quickly identifies and returns our flag.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971916732/uUCiF10xL.png)

We can do the same in hashcat with this command:

```
hashcat -m 0 48bb6e862e54f2a795ffc4e541caed4d rockyou.txt
```

Let’s understand this command before we look at the result. Below are excerpts from the manual with option flags of interest as well as the syntax.

```
dave@ubuntu:~$ man hashcat  
NAME  
       hashcat - Advanced CPU-based password recovery utility  
SYNOPSIS  
       hashcat [options] hashfile [mask|wordfiles|directories]  
[....]  
OPTIONS  
[....]  
       -m, --hash-type=NUM  
              Hash-type, see references below  
       -a, --attack-mode=NUM  
              Attack-mode, see references below
```

So in our earlier command we set our hash-type with the -m flag set to 0 or md5.

This table of example hashes from [hashcat.net](http://hashcat.net) was helpful in quickly identifying which (-m) NUM corresponds to which hash-type, consider bookmarking for later.

[example\_hashes \[hashcat wiki\]](https://hashcat.net/wiki/doku.php?id=example_hashes)

We directly pasted our hash into the terminal in our example but from here on out I will save it into a file before running the command on it, I will show this in the next example but after that you will be expected to know that step is included.

The last part of our command is the path to our wordlist which we downloaded with wget earlier.

*OKAY, IF YOU ARE STILL WITH ME THAN THANK YOU,* **LET’S GET TO THE RESULT OF OUR COMMAND:**

```
dave@ubuntu:~$ hashcat -m 0 48bb6e862e54f2a795ffc4e541caed4d rockyou.txt  
		hashcat (v5.1.0) starting...  
		[....]  
		48bb6e862e54f2a795ffc4e541caed4d:easy  
		Session..........: hashcat  
		Status...........: Cracked  
		Hash.Type........: MD5  
		Hash.Target......: 48bb6e862e54f2a795ffc4e541caed4d  
		Progress.........: 176128/14344387 (1.23%)
```

Now we have cracked it with both crackstation and hashcat and gained a basic understanding of how hashcat works.

Time to submit our flag ‘easy’ and move on.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971917776/oHMbTj6_a.png)

### \[1–2\]

Hash: **CBFDAC6008F9CAB4083784CBD1874F76618D2A97**   
Type: sha1

CrackStation

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971918975/IFwNmxM_t.png)

Quickly put hash into a file:

```
echo "CBFDAC6008F9CAB4083784CBD1874F76618D2A97" > hash.txt
```

hashcat command with sha1 hash-type:

```
hashcat -m 100 hash.txt rockyou.txt
```

And our output as expected:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971920030/2U_8YdLMs.png)

We found our flag ‘password123’.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971921065/3K_U83YCZ.png)

### \[1–3\]

Hash: **1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032**   
Type: sha256

CrackStation

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971922125/_VtGZtcyTB.png)

hashcat command:

```
hashcat -m 1400 hash.txt rockyou.txt
```

Output:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971923144/Gsm1xAp-a.png)

Got the flag, ‘letmein’.

### \[1–4\]

Hash: **$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom**   
Type: BCrypt

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971924354/NnwTdxGq2.png)

Through a bit of googling I figured out this is a bcrypt hash.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971925524/98IV-p1mVw.png)

The next issue I ran into was using echo on this hash string which contains $ characters that are special characters in bash. We could add an escape character \\$ like so before each but that is 3 extra characters.

From [StackOverflow](https://stackoverflow.com/questions/16445292/how-to-print-literal-string-1-in-bash-script):

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971926605/wil-twuxw.png)

Shoutout to JeremyP in the comments, we can just enclose it in ‘single quotes’ to have the same effect.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971927777/56qC8Vzps.png)

CrackStation

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971928890/fMdvaeicc.png)

hashcat command:

```
hashcat -m 3200 hash.txt rockyou.txt
```

This had an expected completion time of multiple days, but the hint gives good information:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971929978/9G9CyrNcv.png)

Found this on [StackOverflow](https://stackoverflow.com/questions/72264342/how-can-i-filter-a-wordlist-to-only-include-words-of-a-certain-minimum-size), to be easily repurposed:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971931051/-Nbxj98mY.png)

Our command:

```
awk 'length($0) == 4' rockyou.txt > rockyou_4chars.txt
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971932161/YcV_O3Jyi.png)

wc -l command counts the number of lines for us, we chopped it down quite a bit.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971933335/c7UdUHja3.png)

At least this one says it will be finished today.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971934479/aw0m0dJeU.png)

And 1 minute, 24 secs later.. we got the flag, ‘bleh’.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971935669/iBJds4dPt.png)

### \[1–5\]

Hash: **279412f945939ba78ce0758d3fd83daa**   
Type: md4

CrackStation

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971936669/MIVvb62Od.png)

Trying to copy how we previously used hashcat I made a similar command:

```
hashcat -m 900 hash.txt rockyou.txt
```

But this just exhausts the list…

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971937689/mGm0S2_jq.png)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971938748/I6m_i7XfH.png)

We need to use a custom rule file, [dive.rule](https://github.com/hashcat/hashcat/blob/master/rules/dive.rule).

Fetch it with wget:

```
wget https://github.com/hashcat/hashcat/raw/master/rules/dive.rule
```

Run our updated hashcat command with -r dive.rule path:

```
hashcat -a 0 -m 900 hash.txt rockyou.txt -r dive.rule
```

Got the flag, ‘Eternity22’.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971939893/C_7jeb1id.png)

Shoutout to john-the-ripper, took me a lot less time to figure it out there:

```
john-the-ripper --format=Raw-MD4 hash.txt
```

### Level 1 Conclusion

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1656971941291/9w2fW5dPP.png)

Success! I hope you got some good information out of this walk-through of TryHackMe’s Crack the hash Level 1. Level 2 coming tomorrow. See you there!