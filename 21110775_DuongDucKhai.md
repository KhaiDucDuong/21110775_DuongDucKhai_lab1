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
*nasm -f elf32 shell.asm -o shell.o*<br>
*ld -m elf_i386 shell.o -o shell*<br>
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/e7300f5a-bcdf-4f50-ad41-6445c6cf36bd"><br>
## 2. Compile C program to executable code:
*We use gcc to compile to given C program (the C program is named vul1.c):*<br>
*gcc -g vul1.c -o vul1.out -fno-stack-protector -mpreferred-stack-boundary=2*<br>
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/e079c512-33b4-4599-a7fa-c1dd22173ebb"><br>


