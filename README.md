# **PKI Lab – Public Key Encryption and Public-Key Infrastructure**

## **Lab Objective**

The goal of this lab is for students to gain **hands-on experience with public-key encryption and PKI concepts**. By the end of this lab, students will be able to:

* Understand public-key cryptography and private/public key pairs
* Encrypt and decrypt messages using public/private keys
* Create and verify digital signatures
* Generate public-key certificates
* Set up a simple Certificate Authority (CA)
* Authenticate users using PKI
* Create secure communication channels (TLS) using certificates

---

## **Prerequisites**

* Linux/Ubuntu environment (or WSL with Ubuntu)
* OpenSSL installed

  ```bash
  sudo apt update
  sudo apt install openssl -y
  ```

---

## **1. Generate a Public/Private Key Pair**

```bash
# Generate a 2048-bit RSA private key
openssl genrsa -out private_key.pem 2048

# Extract the public key
openssl rsa -in private_key.pem -pubout -out public_key.pem
```

**Concepts covered:** private/public key pair, public-key encryption.

---

## **2. Encrypt and Decrypt a Message**

```bash
# Create a message
echo "Hello, PKI!" > message.txt

# Encrypt using the public key
openssl rsautl -encrypt -inkey public_key.pem -pubin -in message.txt -out message.enc

# Decrypt using the private key
openssl rsautl -decrypt -inkey private_key.pem -in message.enc -out decrypted_message.txt
cat decrypted_message.txt
```

**Concepts covered:** confidentiality using public-key encryption.

---

## **3. Create and Verify a Digital Signature**

```bash
# Sign a message with the private key
openssl dgst -sha256 -sign private_key.pem -out message.sig message.txt

# Verify the signature using the public key
openssl dgst -sha256 -verify public_key.pem -signature message.sig message.txt
```

**Concepts covered:** digital signature, integrity, authenticity.

---

## **4. Create a Self-Signed Certificate**

```bash
# Generate a certificate signing request (CSR)
openssl req -new -key private_key.pem -out mycsr.csr

# Create a self-signed certificate valid for 1 year
openssl x509 -req -in mycsr.csr -signkey private_key.pem -out mycert.pem -days 365
```

**Concepts covered:** public-key certificate, self-signed certificate.

---

## **5. Set Up a Simple Certificate Authority (CA)**

```bash
# Generate CA private key
openssl genrsa -out ca.key 4096

# Create a self-signed CA certificate
openssl req -x509 -new -nodes -key ca.key -sha256 -days 365 -out ca.crt

# Generate a CSR for a user certificate
openssl req -new -key private_key.pem -out user.csr

# Sign the user certificate using the CA
openssl x509 -req -in user.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out user_cert.pem -days 365 -sha256
```

**Concepts covered:** certificate authority, certificate signing, PKI hierarchy.

---

## **6. Authenticate Using PKI**

```bash
# Verify user certificate against CA
openssl verify -CAfile ca.crt user_cert.pem
```

**Concepts covered:** authentication, certificate validation.

---

## **7. Create a Secure Channel (TLS)**

```bash
# Start a TLS server using your certificate
openssl s_server -cert user_cert.pem -key private_key.pem -CAfile ca.crt -accept 4443

# Connect as a client
openssl s_client \
-connect localhost:4443 \
-cert user_cert.pem \
-key private_key.pem \
-CAfile ca.crt

```

**Concepts covered:** secure communication, TLS, PKI-based authentication.

---

## **Lab Notes**

* Replace file names as needed for multiple users or messages.
* Use `-days` parameter to simulate certificate expiration in advanced exercises.
* Students can experiment with **different encryption algorithms**, like RSA-4096 or ECDSA keys.
* For scripting exercises, try automating key/certificate generation and message encryption/decryption using bash or Python.

---

## **References**

* [OpenSSL Documentation](https://www.openssl.org/docs/)
* [PKI Concepts Overview](https://en.wikipedia.org/wiki/Public_key_infrastructure)
* [Digital Signatures](https://en.wikipedia.org/wiki/Digital_signature)

