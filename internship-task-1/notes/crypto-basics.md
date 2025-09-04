Cryptography Basics:

A. Symmetric vs Asymmetric Encryption
 Encryption:
  - It is the process to encode data securely such that only the authorized user who knows the key/password is     able to retrieve the original data.
  - The data encrypted can be easily transformed into original text using encryption key.
  - It is less secure compared to hashing.It is not of fixed length as it grows with increase of data.
 1. Symmetric Encryption
  - Uses same key for encryption and decryption.  
  - Fast and efficient, but key distribution is a challenge.  
  Common Symmetric Algorithms:
  |AES| 128/192/256 bits | Most widely used, secure and fast |
  |DES| 56 bits | Older, now insecure due to brute-force attacks |
  |3DES| 112/168 bits | Encrypts data three times, stronger than DES but slow and also considered outdated.|
  |Blowfish| Up to 448 bits | Fast, free, variable key length |
  |Twofish| Up to 256 bits | Successor to Blowfish, more secure, flexible |
  Example: -Plaintext: "Hello" -Key: "12345" -Ciphertext (AES): "X1aB2"
 2. Asymmetric Encryption
  - Uses public key for encryption and private key for decryption.  
  - Solves key distribution problem; slower than symmetric.  
  Common Asymmetric Algorithms:
  |RSA| Key exchange, digital signatures | Very widely used, security depends on key         size (2048+ bits) |
  |ECC| Secure key exchange, digital signatures | Uses smaller keys for same security        as RSA |
  Example: Public Key: user_pub.pem -Private Key: user_priv.pem
           Message encrypted with public key → Decrypted only with private key 

B. Hashing
  - Converts data into a fixed-length.
  - One-way function: Cannot reverse to original data.
  - The hash keys are used to check & compare whether the original data matches or not.Eg-Password for login.
  - Used for integrity verification and password storage.
  - More secure than encryption.It is generally small & fixed length as it doesn't grow even if data increases.
  - Common Hash Algorithms: 
  - MD5:128-bit, fast but vulnerable to collisions  
  - SHA-256:256-bit, more secure, widely used  
  - Example: Message: "Hello"
             MD5: 8b1a9953c4611296a827abf8c47804d7
             SHA-256: 185f8db32271fe25f561a6fc938b2e264306ec304eda518007d1764826381969
C. Digital Certificates & SSL/TLS
 1. Digital Certificates
  - A digital certificate is like a virtual ID card for websites or users.
  - It verifies identity and allows secure communication.
  - Issued by Certificate Authorities (CAs) like DigiCert, Let’s Encrypt, etc.
  Contains:
  - Public Key – Used for encryption by others.
  - Owner Information – Domain name, organization, location.
  - Validity Period – Start and expiry dates.
  - CA Signature – Verifies certificate authenticity.
  Example:
  When you visit https://www.google.com, your browser checks Google’s certificate to confirm it’s authentic      before starting encrypted communication.
  Types of Certificates:
  Domain Validation (DV): Verifies control of domain.
  Organization Validation (OV): Verifies domain and organization details.
  Extended Validation (EV): Thorough validation, shows company name in browser bar
 2. SSL/TLS (Secure Sockets Layer / Transport Layer Security)
  - Protocols that encrypt data between client and server.
  - TLS is the modern, more secure version of SSL (SSL 3.0 is outdated).
  - Why SSL/TLS is Important:
    Confidentiality – Data is encrypted, so attackers can’t read it.
    Integrity – Data cannot be modified in transit without detection.
    Authentication – Ensures you are communicating with the intended server.
  - How SSL/TLS Works (Simplified):
    Browser requests secure connection to server (https://).
    Server sends its digital certificate.
    Browser verifies certificate with CA.
    Browser and server agree on a session key (symmetric encryption) using asymmetric encryption (RSA/ECC).
    All further communication is encrypted using the session key.
  - Example in Real Life: Online banking, e-commerce checkout, Gmail, and any HTTPS website use SSL/TLS to protect sensitive data.
    
D. Hands-on: Encrypt & Decrypt Messages Using OpenSSL
 OpenSSL is a powerful toolkit for implementing cryptography in practice. It allows you to encrypt/decrypt messages, generate keys, and create hashes.
 Installing OpenSSL: Linux (Ubuntu/Debian):
   Commands: sudo apt update
             sudo apt install openssl
             openssl version
 a)Symmetric Encryption(AES):
  Step1- Encrypt a file- Command: openssl enc -aes-256-cbc -in message.txt -out message.enc
  Explaination: openssl-Provides cryptographic functions like encryption,decryption,hashing,and key generation.
     enc-Short for symmetric encryption/decryption algorithms.
     aes-256-cbc-Specifies the algorithm and mode: 1.AES-Advanced Encryption Standard 2.256-Key size is 256           bits (strong security) 3.CBC-Cipher Block Chaining mode (encrypts each block with reference to the             previous block, improving security)          
  Step2- Decrypt the file- Command: openssl enc -aes-256-cbc -d -in message.enc -out message_decrypted.txt
  Explaination: d-Stands for decrypt.Without -d, OpenSSL assumes encryption mode by default.
      -in message.enc-Specifies the input file.
      -out message_decrypted.txt-Specifies the output file.
 b)ASymmetric Encryption(RSA):
    Step 1: Generate RSA keys
    openssl genrsa -out private.pem 2048   # Private key
    openssl rsa -in private.pem -pubout -out public.pem  # Public key
    Step 2: Encrypt message using public key
    openssl rsautl -encrypt -inkey public.pem -pubin -in message.txt -out message.enc
    Step 3: Decrypt message using private key
    openssl rsautl -decrypt -inkey private.pem -in message.enc -out message_decrypted.txt
  c)Hashing Example:
     Generate MD5 hash- Command: openssl dgst -md5 message.txt
     Generate SHA-256 hash- Command: openssl dgst -sha256 message.txt
