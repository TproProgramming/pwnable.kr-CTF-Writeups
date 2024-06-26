# Random CTF Challenge Write-Up: pwnable.kr

## Introduction
In this write-up, I will discuss my steps to get the solution to random, a Capture The Flag challenge on pwnable.kr. To complete the challenge you must gain access to the flag. The CTF starts by giving you a little information on how to complete the challenge:

Daddy, teach me how to use random value in programming!
ssh random@pwnable.kr -p2222 (pw:guest)

## Solution Steps
+ ### Access the Secure Shell
```bash
lubuntu@lubuntu~$ ssh random@pwnable.kr -p2222
random@pwnable.kr's password:
```
+ ### Analyze the directory
```bash
random@pwnable:~$ ls -l
total 20
-r--r----- 1 random_pwn root     49 Jun 30  2014 flag
-r-sr-x--- 1 random_pwn random 8538 Jun 30  2014 random
-rw-r--r-- 1 root       root    301 Jun 30  2014 random.c
```
We have read permissions for random & random.c files. We can execute the file named             random. We have no permissions for the flag file.

+ ### Analyze the random.c code
```bash
random@pwnable:~$ cat random.c
#include <stdio.h>

int main(){
        unsigned int random;
        random = rand();        // random value!

        unsigned int key=0;
        scanf("%d", &key);

        if( (key ^ random) == 0xdeadbeef ){
                printf("Good!\n");
                system("/bin/cat flag");
                return 0;
        }

        printf("Wrong, maybe you should try 2^32 cases.\n");
        return 0;
}
```
After analyzing the code I know that to gain access to the flag file I need to find the right key that, when XORed with the random number, results in '0xdeadbeef'. I know that the XOR operation is reversible so if we can find two of the variables (key, random, or 0xdeadbeef), then we can solve for the third. I know that I pass an unsigned integer to the program for the value of key. 

```bash
0xdeadbeef converted to decimal: 3735928559
```
+ ### Modify and run the random.c code in my own enviroment
  I modified the code to print the value of the random number.
  ```bash
  printf("Random: %u\n", random);
  ```
  Output from the modified code:
  ```bash
  Random: 1804289383
  ```
  I double-checked this by running the same code on an online C compiler and got the same 
  output for random. Now I have the decimal values from 0xdeadbeef and random.

+ ### Solve for key
  I created a simple Python code to solve for key.
  ```bash
  deadbeef = 3735928559
  random = 1804289383

  print(f"Deadbeef: {deadbeef}\n")
  print(f"Random: {random}\n")

  xor = deadbeef ^ random 
  print(f"The XOR is: {xor}")
  ```
  #### Output:
  ```bash
  Deadbeef: 3735928559

  Random: 1804289383

  The XOR is: 3039230856
  ```
  Now that I have the value of key, all I need to do is run the executable and capture the flag.

+ ### Capture the flag
```bash
random@pwnable:~$ ./random
3039230856
```
If the XOR of the input key and the random number matches 0xdeadbeef, the program prints "Good!" and accesses the flag using system("/bin/cat flag").

## Conclusion
+ By understanding the XOR operation, performing the inverse XOR calculation, and inputting the correct key, we successfully accessed the flag in the CTF challenge.
+ The rand() function will produce the same sequence of pseudo-random numbers on each run. This is because the random number generator is initialized with a          default seed
