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

![image](https://github.com/user-attachments/assets/56b53d60-5b95-44de-8114-54fc6bc84f66)

![image](https://github.com/user-attachments/assets/a44cf942-e732-40de-91f3-579cfa6edeef)

![image](https://github.com/user-attachments/assets/f93d61e9-43c4-45c7-884d-eae68d366493)

![image](https://github.com/user-attachments/assets/187766cc-6ecc-487d-9d9a-089521863ee7)
