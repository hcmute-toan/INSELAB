<h1 style="color : blue">VanCongToan_22110079</h1>

# CTF LAB

**_Stack frame of ctf.c_**

![image](https://github.com/user-attachments/assets/9f8c7534-94bb-474d-99e0-f14bd051847f)

- Code command in `ctf.c` file

![image](https://github.com/user-attachments/assets/e5c14d08-6b3c-475f-8d93-5ec4a21c9d1d)

- To run the `myfunc` function, we need to create a `buffer overflow` in the running program by overwriting the address of the `myfunc` function into the return address of the `vuln` function.

- Create `flag1.txt` file so that when myfunc function is run it will not report an error

![image](https://github.com/user-attachments/assets/33c989cf-9378-40d5-a9b3-6b2772bfed62)

- Next we need to get the address of the function `myfunc` ,in here will be `0x0804851b`

![image](https://github.com/user-attachments/assets/94b3d745-8631-449b-b4cb-1d87c8bf6c86)

- In order for the function myfunc to run as we intended, we need to set a breakpoint and consider the corresponding values ​​of `p` and `q`.

![image](https://github.com/user-attachments/assets/b434c0ae-1466-41af-aebf-22116d7345f6)

![image](https://github.com/user-attachments/assets/57878467-e3ed-406d-87a7-9d1116d1845e)

- Now we will overwrite the address of the `myfunc` function into the return address of the `vuln` function

![image](https://github.com/user-attachments/assets/9c07c9c1-5612-4232-a8c8-eecab05e18e3)

![image](https://github.com/user-attachments/assets/976419c0-96d9-473d-9599-e1d9035b474c)

![image](https://github.com/user-attachments/assets/50c0c800-468f-481f-8a99-d2417544ffc6)

**_Lab is complete_**
