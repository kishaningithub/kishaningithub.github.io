+++
title = "Openssl by Example"
description = "Openssl by Example"
date = 2018-11-26T22:43:54+01:00
tags = []
categories = []
+++

- [Symmetric key](#symmetric-key)
    - [Encryption](#encryption)
    - [Decryption](#decryption)
- [Asymmetric key](#asymmetric-key)
    - [Key Generation](#key-generation)
        - [Public key](#public-key)
        - [Private key](#private-key)
    - [Encryption](#encryption-1)
    - [Decryption](#decryption-1)
    - [Sending signed messages](#sending-signed-messages)
    - [Reading signed messages](#reading-signed-messages)
    - [Encrypting private key](#encrypting-private-key)
- [Others](#others)
    - [Find openssl version](#find-openssl-version)
    - [List ciphers](#list-ciphers)

## Symmetric key

### Encryption

```bash
$ echo "top secret text" | openssl enc -aes-256-cbc -base64
enter aes-256-cbc encryption password:
Verifying - enter aes-256-cbc encryption password:
<< encrypted text >>
```

### Decryption

```bash
$ echo "<< encrypted text >>" | openssl enc -d -aes-256-cbc -base64
enter aes-256-cbc decryption password:
top secret text
```

## Asymmetric key

### Key Generation

#### Public key

```bash
openssl genrsa -out private.pem 2048
```

#### Private key

```bash
openssl rsa -in private.pem -pubout -out public.pem
```

### Encryption

```bash
openssl rsautl -encrypt -in secret-transmission.txt -out secret-transmission.txt.enc -inkey public.pem -pubin
```

### Decryption

```bash
openssl rsautl -decrypt -in secret-transmission.txt.enc -out secret-transmission.txt -inkey private.pem
```

### Sending signed messages

```bash
openssl rsautl -sign -in secret-transmission.txt -out secret-transmission.txt.enc.signed -inkey private.pem
```

### Reading signed messages

```bash
openssl rsautl -verify -in secret-transmission.txt.enc.signed -out secret-transmission.txt -inkey public.pem -pubin
```

### Encrypting private key

Never store private key in clear text format!

```bash
openssl rsa -in private.pem -des3 -out private-enc.pem
```

## Others

### Find openssl version

```bash
$ openssl version
LibreSSL 2.6.4
```

### List ciphers

```bash
openssl list-cipher-commands
```