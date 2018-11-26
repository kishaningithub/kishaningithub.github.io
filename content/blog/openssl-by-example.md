+++
title = "Openssl by Example"
description = "Openssl by Example"
date = 2018-11-26T22:43:54+01:00
tags = []
categories = []
+++

## Symmetric encryption

### Encrypting

```bash
$ echo "top secret text" | openssl enc -aes-256-cbc -base64
enter aes-256-cbc encryption password:
Verifying - enter aes-256-cbc encryption password:
U2FsdGVkX18duy5myKqv/eGvlEosO4QiDW/qzfQtHOhN9wLq/3MXCR4DdjLo9cHo
```

### Decrypting

```bash
$ echo "U2FsdGVkX1/nTcZWVQStrpPgmUKve90KvyatNhUQVHoOB+MYv9QqEKrHbzKA7Yyk" | openssl enc -d -aes-256-cbc -base64
enter aes-256-cbc decryption password:
top secret text
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