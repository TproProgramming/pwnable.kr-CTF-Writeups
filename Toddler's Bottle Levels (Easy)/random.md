# Random CTF Challenge Write-Up: pwnable.kr

## Introduction
In this write-up, I will discuss my steps to get the solution to random, a Capture The Flag challenge on pwnable.kr. To complete the challenge you must gain access to the flag. The CTF starts by giving you a little information on how to complete the challenge:

Daddy, teach me how to use random value in programming!
ssh random@pwnable.kr -p2222 (pw:guest)

# Solution Steps
+ Access the Secure Shell
```bash
lubuntu@lubuntu~$ ssh random@pwnable.kr -p2222
random@pwnable.kr's password:
```
+ Analyze the directory
