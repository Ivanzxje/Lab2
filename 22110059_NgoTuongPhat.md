22110059_NgoTuongPhat_02FIE
# Task 1: Public-key based authentication 
**Question 1**: 
Implement public-key based authentication step-by-step with openssl according the following scheme.
![alt text](image-1.png)

**Answer 1**:
Step 1: I will Generate an RSA key pair and create challenge message
On the Server:
Create challenge message:
root@DESKTOP-TK13O9C:~# echo "AuthenticationChallenge" > challenge.txt
![image](/image3.png)

 
On the clients:
"Create private and public keys"
openssl genpkey -algorithm RSA -out client_private.pem -pkeyopt rsa_keygen_bits:2048
openssl rsa -pubout -in client_private.pem -out client_public.pem
![image](/image4.png)

# Task 2: Encrypting large message 
Create a text file at least 56 bytes.
**Question 1**:
Encrypt the file with aes-256 cipher in CFB and OFB modes. How do you evaluate both cipher as far as error propagation and adjacent plaintext blocks are concerned. 
**Answer 1**:
First, we create a text file of at least 56 bytes. I created the `message.txt` file with the following content:
"This is a test message for AES encryption using CFB and OFB modes."

Command to create the file: echo "This is a test message for AES encryption using CFB and OFB modes." > message.txt

![image](/image5.png)
# Step 2: Encrypt the file with AES-256 in CFB mode and OFB Mode
After creating the text file, I used the AES-256 algorithm in CFB mode and OFB Mode to encrypt the message.txt file into message_cfb.enc.

Encryption command for AES-256 in CFB mode:
openssl enc -aes-256-cfb -in message.txt -out message_cfb.enc -pass pass:yourpassword -pbkdf2

Encryption command for AES-256 in OFB mode:
openssl enc -aes-256-ofb -in message.txt -out message_ofb.enc -pass pass:yourpassword -pbkdf2
![image](/image6.png)

# Step 3: Decrypt the CFB-encrypted file and the OFB-encrypted file
After encrypting the files, I proceeded to decrypt the message_cfb.enc file to verify the correctness of the encryption. The decrypted file is saved to decrypted_cfb.txt.

Decryption command for AES-256 in CFB mode:
openssl enc -d -aes-256-cfb -in message_cfb.enc -out decrypted_cfb.txt -pass pass:yourpassword -pbkdf2

Similarly to CFB, I decrypted the message_ofb.enc file with OFB mode and saved the result to decrypted_ofb.txt.

Decryption command for AES-256 in OFB mode:
openssl enc -d -aes-256-ofb -in message_ofb.enc -out decrypted_ofb.txt -pass pass:yourpassword -pbkdf2
![image](/image7.png)

# Step 4: Check the encrypted and decrypted files
After encryption and decryption, I checked the size and content of the resulting files.
### Check the encrypted files:
- **File size of `message_cfb.enc`:**
  - `-rw-r--r-- 1 root root 83 Nov 26 10:00 message_cfb.enc`
  
- **File size of `message_ofb.enc`:**
  - `-rw-r--r-- 1 root root 83 Nov 26 10:00 message_ofb.enc`

### Check the decrypted files:
- **File size of `decrypted_cfb.txt`:**
  - `-rw-r--r-- 1 root root 67 Nov 26 10:00 decrypted_cfb.txt`

- **File size of `decrypted_ofb.txt`:**
  - `-rw-r--r-- 1 root root 67 Nov 26 10:00 decrypted_ofb.txt`

### Check the content of the decrypted files:
- **Content of `decrypted_cfb.txt`:**
  - `This is a test message for AES encryption using CFB and OFB modes.`

- **Content of `decrypted_ofb.txt`:**
  - `This is a test message for AES encryption using CFB and OFB modes.`

root@DESKTOP-TK13O9C:~# ls -lh message_cfb.enc
-rw-r--r-- 1 root root 83 Nov 26 10:00 message_cfb.enc
root@DESKTOP-TK13O9C:~# ls -lh message_ofb.enc
-rw-r--r-- 1 root root 83 Nov 26 10:00 message_ofb.enc
root@DESKTOP-TK13O9C:~# ls -lh decrypted_cfb.txt
-rw-r--r-- 1 root root 67 Nov 26 10:00 decrypted_cfb.txt
root@DESKTOP-TK13O9C:~# ls -lh decrypted_ofb.txt
-rw-r--r-- 1 root root 67 Nov 26 10:00 decrypted_ofb.txt
root@DESKTOP-TK13O9C:~# cat decrypted_cfb.txt
This is a test message for AES encryption using CFB and OFB modes.
root@DESKTOP-TK13O9C:~# cat decrypted_ofb.txt
This is a test message for AES encryption using CFB and OFB modes.
root@DESKTOP-TK13O9C:~#

![image](/image8.png)

**Question 2**:
Modify the 8th byte of encrypted file in both modes (this emulates corrupted ciphertext).
Decrypt corrupted file, watch the result and give your comment on Chaining dependencies and Error propagation criteria.

**Answer 2**:
# Step 1: Modify the 8th byte of the encrypted file
In this step, we simulate a corrupted ciphertext by modifying the 8th byte of both the message_cfb.enc and message_ofb.enc files.
# AES-256 Encryption Error Propagation in CFB and OFB Modes

### **Step 1: Modify the 8th Byte of the Encrypted File**
We simulate a corrupted ciphertext by modifying the 8th byte of both the `message_cfb.enc` and `message_ofb.enc` files.

#### **For CFB Mode:**
1. Use `xxd` to view the file in hexadecimal format:
command: xxd message_cfb.enc

#### **For OFB Mode:**
Use xxd to view the file in hexadecimal format:
command: xxd message_ofb.enc
and then we have
root@DESKTOP-TK13O9C:~# xxd message_cfb.enc
00000000: 5361 6c74 6564 5f5f 87cf d174 90cb 00d0  Salted__...t....
00000010: b4e3 2a05 ffd5 382b edd3 6106 016e 8f7b  ..*...8+..a..n.{
00000020: 3fa8 e58d 4621 2d16 0571 eb4e c6b3 7e56  ?...F!-..q.N..~V
00000030: ce2e 46e7 1c12 485e 2e0b 92fd a5f2 ad9e  ..F...H^........
00000040: c375 acbb c2bd ca5c 8cb2 43f9 e7d4 b398  .u.....\..C.....
00000050: 94ee 21                                  ..!
The 8th byte in this file is 00 (from the 90cb 00d0 part).
root@DESKTOP-TK13O9C:~# xxd message_ofb.enc
00000000: 5361 6c74 6564 5f5f 81ae 2687 2ed6 1044  Salted__..&....D
00000010: b112 3552 f22a c490 795c d89f df20 eb53  ..5R.*..y\... .S
00000020: 8a36 6f10 50fe 589c 8ffa cdec e7ab 3ea0  .6o.P.X.......>.
00000030: e657 9c99 580a 6bb9 3bdf e09d b5f0 6fe3  .W..X.k.;.....o.
00000040: 2ca2 77bb d612 a0da 27d5 c2fa 7d83 a31e  ,.w.....'...}...
00000050: d1cb a9                                  ...
The 8th byte is the second byte in the sequence 81ae. So, the 8th byte is 0x81.
![image](/image9.png)

### Step 2: Modify the 8th Byte
### For message_cfb.enc:
- Modify the byte with xxd:
Convert the file to a hex format:
Command: *xxd message_cfb.enc > message_cfb.hex*
- Edit the hex file using a text editor: Open the file in a text editor:
Command: *vim message_cfb.hex*
Modify the 8th byte (the 00 byte) to a new value by FF
00000000: 5361 6c74 6564 5f5f 87cf d174 90cb 00d0  Salted__...t....
To:
00000000: 5361 6c74 6564 5f5f 87cf d174 90cb FFD0  Salted__...t....
then press ESC and type :wq to save and close 
![image](/image10.png)
![image](/image11.png)
![image](/image12.png)

### Step 2: Convert Hex File Back to Binary Format
After modifying the byte, convert the file back to binary format to create the corrupted encrypted file:
Command: xxd -r message_cfb.enc > message_cfb_corrupted.enc
This will generate the corrupted file message_ofb_corrupted.enc.

### Step 3: Decrypt the Corrupted File
Now, decrypt the corrupted file using OpenSSL:
Command: openssl enc -d -aes-256-cfb -in message_cfb_corrupted.enc -out decrypted_cfb_corrupted.txt -pass pass:yourpassword -pbkdf2
![image](/image15.png)


### For message_ofb.enc:
### Step 1:
- Modify the byte with xxd:
Convert the file to a hex format:
Command: *xxd message_ofb.enc > message_ofb.hex*
- Edit the hex file using a text editor: Open the file in a text editor:
Command: *vim message_ofb.hex*
In the editor, Find the 8th byte (0x81) in the hex dump. It appears in the line:
00000000: 5361 6c74 6564 5f5f 81ae 2687 2ed6 1044  Salted__..&....D
![image](/image13.png)

To:
Change the 8th byte (81) to another value, I will choose 0xFF. After modification, the hex dump should look like this:
00000000: 5361 6c74 6564 5f5f FFAE 2687 2ed6 1044  Salted__..&....D
then press ESC and type :wq to save and close 
![image](/ofb1.png)

### Step 2: Convert Hex File Back to Binary Format
After modifying the byte, convert the file back to binary format to create the corrupted encrypted file:
Command: xxd -r message_ofb.enc > message_ofb_corrupted.enc
This will generate the corrupted file message_ofb_corrupted.enc.

### Step 3: Decrypt the Corrupted File
Now, decrypt the corrupted file using OpenSSL:
Command: openssl enc -d -aes-256-ofb -in message_ofb_corrupted.enc -out decrypted_ofb_corrupted.txt -pa
![image](/cfb.png)

### Step 5: Check the Decrypted Content
Check the content of the decrypted files to observe how the 8th byte modification affected the decryption process:
- Command i use:
cat decrypted_cfb_corrupted.txt
![image](/cfbans.jpg)
cat decrypted_ofb_corrupted.txt
result: 
![image](/ofb2.png)
