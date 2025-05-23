---
layout: post
title: SSL Certificate Management - Quick Reference
author: gini
categories: [ security]
image: "assets/images/2020/jon-moore-bBavss4ZQcA-unsplash-locks.jpg"
tags: [cloud, automation, containers, kubernetes]
permalink: ssl
featured: false
hidden: false
showindex: true
titleshort: ssl
---

- [1. Create a Root CA](#1-create-a-root-ca)
- [2. Trusting new RootCA in system](#2-trusting-new-rootca-in-system)
- [3. Create Key, CSR and Certificate](#3-create-key-csr-and-certificate)
  - [3.1. Create a new SSL Key for server/application](#31-create-a-new-ssl-key-for-serverapplication)
  - [3.2. Generate Certificate Signing Request (CSR)](#32-generate-certificate-signing-request-csr)
  - [3.3. Generate `myserver.crt` Certificate using CSR and CA](#33-generate-myservercrt-certificate-using-csr-and-ca)
  - [Verify certificate content](#verify-certificate-content)
- [4. How to verify SSL Certificates](#4-how-to-verify-ssl-certificates)
  - [4.1. Verify Certificate and Key](#41-verify-certificate-and-key)
  - [4.2. Change or remove passhphrase](#42-change-or-remove-passhphrase)
  - [4.3. Extract from PFX file](#43-extract-from-pfx-file)
  - [4.4. Extract Certificate from P7B file](#44-extract-certificate-from-p7b-file)
- [5. References](#5-references)

## 1. Create a Root CA

```shell
$ mkdir RootCA && cd RootCA

# Create RootCA Key
$ openssl genrsa -des3 -out rootCA.key 4096
```

Remove the `-des3` option for non-password protected key.

The -des3 option specifies how the private key is encrypted with a password. Without a cipher option, the private key is not encrypted, and no password is required.

Note: Optionally you can create the RootCA.csr and sign it.

```shell
$ openssl req -new -key ca.key -subj "/CN=MYROOT-CA" -out rootCA.csr
$ openssl x509 -req -in rootCA.csr -signkey rootCA.key -out rootCA.crt
```

```shell
# Create self-sign CA Certificate with 10 years validity
$ openssl req -x509 -new -nodes -key rootCA.key -sha512 -days 3650 -out rootCA.pem
```

## 2. Trusting new RootCA in system

Eg: Fedora/RHEL

```shell
$ sudo cp ../RootCA/rootCA.pem /etc/pki/ca-trust/source/anchor
s/iamgini-rootCA.pem

# make sure the certificate is made available through the /etc/pki/ca-trust/extracted
$ sudo update-ca-trust

# Verify Root CA - List trust or certificates
$ trust list
```


## 3. Create Key, CSR and Certificate

Optional - create a folder to avoid overwrite.

```shell
$ cd .. && mkdir SSL_CERTS  && cd SSL_CERTS
```

### 3.1. Create a new SSL Key for server/application

```shell
$ openssl genrsa -out myserver.key 4096
```

### 3.2. Generate Certificate Signing Request (CSR)

```shell
$ openssl req -new \
  -key myserver.key \
  -out myserver.csr \
  -subj "/C=SG/ST=Singapore/L=CBD/O=iamgini/OU=IT/CN=aap25.lab.iamgini.com/emailAddress=gini@iamgini.com"
```

Note: Adding `-nodes` (means no DES) tells OpenSSL not to encrypt the private key.

Note: You can also create a new key and CSR together as follows.

```shell
$ openssl req -newkey rsa:4096 \
  -keyout myserver.key \
  -out myserver.csr \
  -nodes \
  -subj "/C=SG/ST=Singapore/L=CBD/O=iamgini/OU=IT/CN=aap25.lab.iamgini.com/emailAddress=gini@iamgini.com"
```

If you want interactive entry for CSR details, then you can use the following command.

```shell
$ openssl req -newkey rsa:4096 \
  -keyout myserver.key \
  -out myserver.csr \
  -nodes
```

### 3.3. Generate `myserver.crt` Certificate using CSR and CA

```shell
$ openssl x509 -req \
  -CA ../RootCA/rootCA.pem \
  -CAkey ../RootCA/rootCA.key \
  -CAcreateserial \
  -in myserver.csr \
  -out myserver.crt \
  -days 1825 -sha512
```

### Verify certificate content

```shell
$ openssl x509 -in myserver.crt -text -noout
```

You can also verify the key and certificate matching using md5 as follows.

```shell
$ openssl rsa -noout -modulus -in myserver.key | openssl md5
MD5(stdin)= 22c83dc812316f248c20ba345410fb77

$ openssl x509 -noout -modulus -in myserver.crt | openssl md5
MD5(stdin)= 22c83dc812316f248c20ba345410fb77
```

Or match it using the fingerprint.

```shell
$ openssl rsa -in myserver.key -pubout -outform PEM | openssl sha256
writing RSA key
SHA2-256(stdin)= df3581f4be1929b53745c5cda83e49914382c9f48fff39d83936ea76d8ccfc56

$ openssl x509 -in myserver.crt -pubkey -noout | openssl sha256
SHA2-256(stdin)= df3581f4be1929b53745c5cda83e49914382c9f48fff39d83936ea76d8ccfc56
```


Check a PKCS#12 file (.pfx or .p12)

```shell
$ openssl pkcs12 -info -in keyStore.p12
```

Check and verify Key file

```shell
$ openssl rsa -in server.key -check
```

Verify CSR content

```shell
$ openssl req -in server.csr -noout -text
$ openssl req -in server.csr -noout -text -verify
```

```shell
## Generate Certificate using CSR and CA
## openssl x509 -req -in <CSR FILE> \
##   -CA <CA FILE> -CAkey myserver-CA.key -CAcreateserial \
##   -passin file:passphrase.txt \
##   -out <EXPORT CRT> -days 3650 -sha256 -extfile myserver.ext
$ openssl x509 -req \
  -passin file:passphrase.txt \
  -CA myserver-CA.pem -CAkey myserver-CA.key -CAcreateserial \
  -in myserver.csr \
  -out myserver.crt \
  -days 1825 -sha256 -extfile myserver.ext

## verify certificte content
$ openssl x509 -in myserver.crt -text -noout
```

## 4. How to verify SSL Certificates

### 4.1. Verify Certificate and Key

You should get the same `md5` output for all commands.

```shell
# certificate
$ openssl x509 –noout –modulus –in <file>.crt | openssl md5

# key
$ openssl rsa –noout –modulus –in <file>.key | openssl md5

# csr
$ openssl req -noout -modulus -in <file>.csr | openssl md5
```

Check the Key only

```shell
$ openssl rsa -check -noout -in myserver.key
RSA Key is ok
```

### 4.2. Change or remove passhphrase

Remove Passphrase from SSL key

```shell
$ openssl rsa -in original.key -out new.key
```

Change the passphrase of the SSL Key

```shell
$ openssl rsa -aes256 -in original.key -out new.key
```


### 4.3. Extract from PFX file

```shell
$ openssl pkcs12 -in [yourfile.pfx] -nocerts -out [drlive.key]
```

### 4.4. Extract Certificate from P7B file

```shell
$ openssl pkcs7 -inform PEM -outform PEM -in certnew.p7b -print_certs > certificate.cer
```


## 5. References

- [OpenSSL Commands](https://pleasantpasswords.com/info/pleasant-password-server/b-server-configuration/3-installing-a-3rd-party-certificate/openssl-commands)


```shell
 # openssl genrsa -out pgsql.key 4096

 # vi pgsql_csr.conf

   [req]
   default_md = sha512
   prompt = no
   req_extensions = req_ext
   distinguished_name = req_distinguished_name
   [req_distinguished_name]
   commonName = database.ansible.com
   countryName = US
   [req_ext]
   subjectAltName = @alt_names
   [alt_names]
   DNS.0 = database.ansible.com
   IP.0 = 192.168.7.19

 # openssl req -new -nodes -key pgsql.key -config pgsql_csr.conf -out pgsql.csr

 # openssl x509 -req -in pgsql.csr -days 3650 -CA ~/RootCA/rootCA.pem -CAkey ~/RootCA/rootCA.key -CAcreateserial -extensions req_ext -out pgsql.crt -extfile pgsql_csr.conf

Transfer the rootCA.pem to the Controller node

 # cp rootCA.pem /etc/pki/ca-trust/source/anchors/
 # restorecon -Rv /etc/pki/ca-trust/source/anchors/
 # update-ca-trust

```