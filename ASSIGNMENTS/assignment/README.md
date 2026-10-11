### MemLabs Memory Forensics Challenge

**Aims & Objectives**

**Lab 1: Beginner's Luck**

lab description
My sister’s computer crashed. We were very fortunate to recover this memory dump. Your job is get all her important files from the system. From what we remember, we suddenly saw a black window pop up with some thing being executed. When the crash happened, she was trying to draw something. Thats all we remember from the time of crash.

I have a memory dump file that is from MemLabs. lets hava a look at the basic information of the memory dump file.
![alt text](assets/0.png)
```
vol.py -f MemoryDump_Lab1.raw
```
we have to determine a profile of each memory dump and after got the suitable profile for this memory dump.
Now i have to see what was happening or what user was doing at the time of memory dump. I have to see what processes were running at the time of memory dump.

![alt text](assets/1.png)

some of the files that are intresting to investigate on.

lets figure out more from it. 
![alt text](assets/2.png)
this process includes files that are intresting to investigate on. lets figure out more from it. first lets dump the file on disk and then we can analyze it.

![alt text](assets/3.png)
this is the file that extracted from the memory dump. lets see what is inside it.

![alt text](assets/4.png)

![alt text](assets/5.png)
it says "Password is NTLM hash(in uppercase) of Alissa's account passwd."

lets dump all NTLM hash then we should use this hash to view an image file by now.

![alt text](assets/6.png)
![alt text](assets/7.png)
![alt text](assets/flag3.png)
here is the flag for this lab.


![alt text](assets/9.png)
we can even use consoles plugin to see what was happening in these processes.
![alt text](assets/10.png)

![alt text](assets/11.png)
it says "this is the first stage"
now lets hunt for the second stage. we can use the same method to find the second stage. lets see what we can find in the memory dump.

![alt text](assets/8.png)
using the pstree command we can see these 2 processes were running at the time of memory dump. lets see what was happening in these processes. mspaint doesn't have any arguments, means that this file isn't saved lets dump the mspaint process and see what we can find in it.

**MemLabs Lab 2: A New World**

**Description**: One of the clients of our company, lost the access to his system due to an unknown error. He is supposedly a very popular "environmental" activist. As a part of the investigation, he told us that his go to applications are browsers, his password managers etc. We hope that you can dig into this memory dump and find his important stuff and give it back to us.

Lets find important files from the memory dump. we can use the same method to find the important files from the memory dump. lets see what we can find in the memory dump.

![alt text](assets/12.png)
this is the imageinfo

![alt text](assets/13.png)
there are lots of processes running at the time of memory dump. lets see what we can find in these processes. let me look at most valuable processes 

![alt text](assets/14.png)
there must be hidden files in the Hidden.kdbx 

![alt text](assets/15.png)
lets grab its offset to dump the file.

```
                                                                                                                                                                                              
┌──(khem㉿kali)-[~/volatility]
└─$ python2 vol.py -f MemoryDump_Lab2.raw --profile=Win7SP1x64 dumpfiles -D . -Q  0x000000003fb112a0 -n 
Volatility Foundation Volatility Framework 2.6.1
DataSectionObject 0x3fb112a0   None   \Device\HarddiskVolume2\Users\SmartNet\Secrets\Hidden.kdbx

```
now we have that file in our local machine.
the file is locked with password. we need password to unlock it. lets see if we can find the password in the memory dump.

![alt text](assets/16.png)

I am not able to use the envars plugin to find the password. finally after 2 hours of trying different methods, I could run the envars plugin to find the password.

![alt text](assets/17.png)
found the base64 encoded password. lets decode it maybe i wll get some useful stuff

![alt text](assets/18.png)
ohh! it is flag 1

here are the hashes 
![alt text](assets/19.png)
now we will find the file related to this users 
![alt text](assets/20.png)
now lets dump the file and see what is inside it.
![alt text](assets/21.png)

I got the file and i don't know how to open it.
![alt text](assets/22.png)

it says "Psst!! password is P4SSw0rd_123"

may be it could be the password for the database file. lets try to open the database file with this password.

![alt text](assets/23.png)
the password didn't work for me 

Lets move to hunt for the next flag. in the challenge it says "go to browser application"

![alt text](assets/24.png)
browser history will be stored in the History file. lets dump the history file and see what we can find in it.

![alt text](assets/25.png)

i dump the history file and go through the url and found the ineteresting url 
![alt text](assets/26.png)
when i visit the url there is zip file and it is stage 3 of lab 2

![alt text](assets/27.png)
info: "Password is SHA1(stage-3-FLAG) from Lab-1. Password is in lowercase."
```
flag{w3ll_3rd_stage_was_easy}
```
![alt text](assets/29.png)
i converted the password to SHA1 and got the password for the zip file. i extracted the zip file and got the flag 3 of lab 2
![alt text](assets/28.png)
this is the last state of lab 2

I couldn't get the one of the flag because I was not able to open the database file tough I got the password for it. 

**Lab 3: The Final Stage: The Evil's Den**

**Challenge Description**:
A malicious script encrypted a very secret piece of information I had on my system. Can you recover the information for me please?

Note-1: This challenge is composed of only 1 flag. The flag split into 2 parts.

Note-2: You'll need the first half of the flag to get the second.

![alt text](assets/30.png)
basic info about the .raw file

listing the processes
![alt text](assets/31.png)

i have to find the script that encrypted the file. lets find the it 
we can use the cmdline plugin to find the script that encrypted the file.
![alt text](assets/32.png)
and here is it the evilscript.py and vip.txt this doesn't look like a normal file.

lets use filescan plugin to find the suspicous files and we can get its offset

![alt text](assets/33.png)
i have dumped both the files evilscript.py and vip.txt

![alt text](assets/34.png)

in the vip.txt there is base64 characters and when it decode it, it gave wired output "jm`wex3m0\k7oe"

![alt text](assets/35.png)

the script is reading vip.txt and xor it with key given by the user and use encoder function to encode ciphertext from the xor base64 then write that output to vip.txt replacing the old content 

![alt text](assets/36.png)

got the first half of the flag "inctf{0n3_h4lf"
To move to the next step we need the steghide, so the steghide are mostly used for the image. so lets find image in the filescan list

![alt text](assets/37.png)
first i searched for the jpg and there was nothing suspicous but when i searched for the jpeg there was one file "suspision1.jpeg"

![alt text](assets/38.png)
finally after trying multiple tools because i missed the hint given in the challenge description, i was able to extract the second half of the flag from the image file. passphrass to extract was the first half of the flag.

flag: "inctf{0n3_h4lf_1s_n0t_3n0ugh}

**Lab 4: The Final Stage: The Evil's Den**

