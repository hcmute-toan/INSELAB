# Lab #2,20110079, Van Cong Toan,INSE33030E_02FIE

---

# Lab environment

- Create 2 virtual machines `kali` and `ubuntu` to do task1, `kali` is the `client side` representative machine and `ubuntu` is the `server side` representative machine
  ![image](https://github.com/user-attachments/assets/1e6bc70d-bbe3-4c78-853c-3dd2e340764a)
  ![image](https://github.com/user-attachments/assets/b933813c-ba82-4209-a541-2e8f9e554656)

# Task 1: Public-key based authentication

## **Question 1**:

Implement public-key based authentication step-by-step with openssl according the following scheme.
![image](https://github.com/user-attachments/assets/bf68cf8c-f9bd-4f0b-9fb5-07da723a0097)

## **Answer 1**:

### Step 1: Generate Key Pairs

1.Generate the server's private and public key pair:

```bash
openssl genpkey -algorithm RSA -out server_private.pem -pkeyopt rsa_keygen_bits:2048
openssl rsa -pubout -in server_private.pem -out server_public.pem
```

![image](https://github.com/user-attachments/assets/ce69aa7a-fd4c-4d76-b276-b044488c4af2)
2.Generate the client's private and public key pair:

```bash
openssl genpkey -algorithm RSA -out client_private.pem -pkeyopt rsa_keygen_bits:2048
openssl rsa -pubout -in client_private.pem -out client_public.pem
```

![image](https://github.com/user-attachments/assets/321d4daa-811d-4377-9c7b-25c79924362f)

### Step 2: Share Public Keys

Before sharing a file, we need to know the IP address and path to the recipient's file.

- server
  ![image](https://github.com/user-attachments/assets/b7922865-0eb8-4e8c-8bbd-dd24ac846981)
- client
  ![image](https://github.com/user-attachments/assets/34cabbc8-0f73-413c-a66c-526a34b27e18)

The client shares client_public.pem with the server.
![image](https://github.com/user-attachments/assets/860e7704-dca4-49cf-8695-a498160cadf6)
![image](https://github.com/user-attachments/assets/7ab17150-7dac-460a-8539-7f2346615c60)
The server shares server_public.pem with the client.
![image](https://github.com/user-attachments/assets/3c492f66-80dd-4151-ae25-e9f21e7aeda8)
![image](https://github.com/user-attachments/assets/ec884405-6b04-4355-92f7-f0beac99f59e)

### Step 3: Client Sends Encrypted Message

The client creates a message (e.g., "Hello Server").

```bash
echo "Hello Server" > message.txt
```

![image](https://github.com/user-attachments/assets/355271a3-3905-4322-931d-aead71016345)

Encrypt the message using the server's public key (ensures only the server can decrypt it):

```bash
openssl rsautl -encrypt -inkey server_public.pem -pubin -in message.txt -out encrypted_message.bin
```

![image](https://github.com/user-attachments/assets/3281b350-21ff-40e9-80d0-eb40f62044c3)
The client sends encrypted_message.bin to the server.
![image](https://github.com/user-attachments/assets/b364d5ad-d66e-4c37-8c57-8fdab2a8616b)
![image](https://github.com/user-attachments/assets/3995fab3-a4af-4c6a-8527-5291b4a2edba)

### Step 4: Server Decrypts the Message

1.The server decrypts the message using its private key:

```bash
openssl rsautl -decrypt -inkey server_private.pem -in encrypted_message.bin -out decrypted_message.txt
```

2. Check the decrypted message:

```bash
cat decrypted_message.txt
```

![image](https://github.com/user-attachments/assets/5fd15757-7b76-4a03-b9a1-136688029632)

### Step 5: Server Sends Signed Response

1.The server creates a response message (e.g., "Hello Client"):

```bash
echo "Hello Client" > response.txt
```

2.Sign the message using the server's private key:

```bash
openssl dgst -sha256 -sign server_private.pem -out response_signature.bin response.txt
```

![image](https://github.com/user-attachments/assets/db68b3af-2a8b-4dd4-bcf1-fdec1d85c966)
3.The server sends both response.txt and response_signature.bin to the client.
![image](https://github.com/user-attachments/assets/9a45bad8-5d73-4f82-9238-fd826b4ce255)
![image](https://github.com/user-attachments/assets/4f950303-2668-421d-ba81-dc0607120b52)

### Step 6: Client Verifies the Response

The client verifies the signature using the server's public key:

```bash
openssl dgst -sha256 -verify server_public.pem -signature response_signature.bin response.txt
```

If the output shows Verified OK, the response is authenticated.
![image](https://github.com/user-attachments/assets/7d8fff2e-784b-4cbc-9687-73435f749f29)

# Task 2: Encrypting large message

Create a text file at least 56 bytes.

## **Question 1**:

Encrypt the file with aes-256 cipher in CFB and OFB modes. How do you evaluate both cipher as far as error propagation and adjacent plaintext blocks are concerned.

## **Answer 1**:

### Step 1: Create a Text File

We need a file with at least 56 bytes of content. You can create it using the echo command or any text editor:

```bash
echo "This is a test message to demonstrate AES-256 encryption modes." > large_message.txt
```

Ensure the file contains 56 or more bytes:

```bash
wc -c large_message.txt
# Output: 64 large_message.txt (64 bytes in this case)
```

![image](https://github.com/user-attachments/assets/d0ed9092-a984-4929-95fc-061660a520d9)

### Step 2: Encrypt the File in CFB and OFB Modes

We will use the openssl command-line tool to encrypt the file in CFB and OFB modes with AES-256.

Encrypt with AES-256 in CFB mode:

```bash
openssl enc -aes-256-cfb -in large_message.txt -out encrypted_cfb.bin -pass pass:yourpassword
```

Encrypt with AES-256 in OFB mode:

```bash
openssl enc -aes-256-ofb -in large_message.txt -out encrypted_ofb.bin -pass pass:yourpassword
```

Check the outputs:

```bash
ls
```

![image](https://github.com/user-attachments/assets/b8e4d525-b9b5-45a8-94dd-f90ae23f67e7)

### Step 3: Evaluate Error Propagation

**CFB Mode:**

CFB mode uses the ciphertext of the previous block for encrypting the current block.
If one byte of ciphertext is corrupted, it will affect both the current plaintext byte and the next byte during decryption.
Error propagation exists in CFB mode.

**OFB Mode:**

OFB mode uses a feedback mechanism that generates a keystream independently of the plaintext or ciphertext.
If one byte of ciphertext is corrupted, only that plaintext byte is affected.
There is no error propagation in OFB mode.

## **Question 2**:

Modify the 8th byte of encrypted file in both modes (this emulates corrupted ciphertext).
Decrypt corrupted file, watch the result and give your comment on Chaining dependencies and Error propagation criteria.

## **Answer 2**:

### Step 1: Modify the 8th Byte of Each Encrypted File

To simulate corrupted ciphertext, modify the 8th byte of the encrypted files using the xxd command:

Modify the CFB-encrypted file:

```bash
xxd encrypted_cfb.bin > temp_cfb.hex
nano temp_cfb.hex
# Find the 8th byte (line 0x00000010, column 8) and modify it.
xxd -r temp_cfb.hex encrypted_cfb_corrupted.bin
```

![image](https://github.com/user-attachments/assets/273d4d05-2cf0-4415-a671-5c7e7eccecae)
![image](https://github.com/user-attachments/assets/0791e38e-1985-4e45-9773-f3a7c9336a01)
![image](https://github.com/user-attachments/assets/519803db-39a4-4160-9e4b-ffb261f1d9a7)
Modify the OFB-encrypted file:

```bash
xxd encrypted_ofb.bin > temp_ofb.hex
nano temp_ofb.hex
# Find the 8th byte (line 0x00000010, column 8) and modify it.
xxd -r temp_ofb.hex encrypted_ofb_corrupted.bin
```

![image](https://github.com/user-attachments/assets/7cccaeb3-9916-4b3d-8e4a-2f0c5753fef7)
![image](https://github.com/user-attachments/assets/880a3004-5c47-4a0d-a7ac-acc038b66dcd)
![image](https://github.com/user-attachments/assets/8805136c-1685-44ed-837f-32d3ed78f8ae)

### Step 2: Decrypt the Corrupted Files

Now decrypt the corrupted files and observe the results.

Decrypt the corrupted CFB file:

```bash
openssl enc -aes-256-cfb -d -in encrypted_cfb_corrupted.bin -out decrypted_cfb.txt -pass pass:yourpassword
cat decrypted_cfb.txt
```

![image](https://github.com/user-attachments/assets/037cdc8b-5d8b-48d9-88c0-42beff50ae51)
Decrypt the corrupted OFB file:

```bash
openssl enc -aes-256-ofb -d -in encrypted_ofb_corrupted.bin -out decrypted_ofb.txt -pass pass:yourpassword
cat decrypted_ofb.txt
```

![image](https://github.com/user-attachments/assets/cad76d01-70bd-4c51-bf2a-b44b14810541)

### Step 3: Compare and Analyze

**CFB Mode:**

If you modify the 8th byte, the 8th plaintext byte and the 9th plaintext byte will be corrupted. This demonstrates error propagation because each ciphertext block depends on the previous block.

**OFB Mode:**

Modifying the 8th byte will corrupt only the 8th plaintext byte. The rest of the plaintext remains intact. This shows that OFB mode does not propagate errors.
