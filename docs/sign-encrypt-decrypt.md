<h1 align="center">🔐 YubiKey Reference</h1>
<p align="center">
    <em>The Ultimate Guide to Using YubiKey for GPG and SSH</em>
</p>

<p align="center">
    <a href="https://github.com/TechProber/yubikey-reference/blob/master/LICENSE">
      <img src="https://img.shields.io/github/license/TechProber/yubikey-reference?color=critical" alt="License"/>
    </a>
    <a href="https://hits.seeyoufarm.com">
      <img src="https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2FTechProber%2Fyubikey-reference&count_bg=%235322B2&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false"/>
    </a>
    <a href="https://img.shields.io/tokei/lines/github/TechProber/yubikey-reference?color=orange">
      <img src="https://img.shields.io/tokei/lines/github/TechProber/yubikey-reference?color=orange" alt="lines">
    </a>
    <a href="https://www.yubico.com/blog/yubikey-firmware-update-yubikey-5-series-with-firmware-5-4/">
        <img src="https://img.shields.io/badge/yubikey--firmware-v5.4.3-brightgreen" alt="Firmware Version">
    </a>
</p>

## Introduction

CopyRight 2022 TechProber. All rights reserved.

Maintainer: [ Kevin Yu (@yqlbu) ](https://github.com/yqlbu)

This repo serves to provide the end-users a way to speed up their Yubikey configuration process

**Notes:** This guide temporarily ONLY supports `Archlinux User`

## Table of Contents

- [Home Page](https://github.com/TechProber/yubikey-reference)
- [GPG pull public key from keyserver](#gpg-pull-public-key-from-keyserver)
- [GPG signing](#gpg-signing)
- [GPG export public key to keyserver](#gpg-export-public-key-to-keyserver)
- [GPG encryption](#gpg-encrypt)
- [GPG decryption](#gpg-decrypt)

## GPG pull public key from keyserver

Notes: the following command will pull the target public key (including fingerprint) from keyserver

```bash
gpg --rec-keys <key id>
```

## GPG signing

### GPG sign a recipient's public key

Once you know a public key is used for encryption belongs to the person you think it does, you may sign it with the following command:

```
gpg --sign-key <recepient's email>
```

Before signing the key though, how do you know it can be trusted? If the intended recipient sent you the key directly, you can be sure, provided they are a trusted party. Else, you may ask the intended recipient to send you the fingerprint of the key. This can be retrieved by running the following command:

```
gpg --fingerprint <recepient's email>
```

## GPG export public key to keyserver

```bash
gpg --send-keys <key id>

# with a specific keyserver
gpg --key-server 'hkps://pgp.mit.edu' --send-keys <key id>
```

Refresh existing public keys

```bash
gpg --keyserver 'hkps://pgp.mit.edu' --refresh-keys
```

## GPG encrypt

Reference: https://www.misterpki.com/gpg-encrypt/

### GPG trust a recipient's public key

```
gpg --edit-key <recepient's public key>

# select the key with its index number
gpg> 1

pub  rsa4096/89CE9931267F4CE8
     created: 2020-07-10  expires: never       usage: C
     trust: ultimate      validity: ultimate
sub  rsa4096/1C8EDC693A7E0B8E
     created: 2020-07-10  expires: never       usage: A
sub  rsa4096/C4C43816516A9059
     created: 2020-07-10  expires: never       usage: E
sub  rsa4096/DE45A3E23F339456
     created: 2020-07-10  expires: never       usage: S
[ultimate] (1)* Chris (I'm free man) <chris@lesscrowds.org>

# set trust level
gpg> trust

pub  rsa4096/89CE9931267F4CE8
     created: 2020-07-10  expires: never       usage: C
     trust: ultimate      validity: ultimate
sub  rsa4096/1C8EDC693A7E0B8E
     created: 2020-07-10  expires: never       usage: A
sub  rsa4096/C4C43816516A9059
     created: 2020-07-10  expires: never       usage: E
sub  rsa4096/DE45A3E23F339456
     created: 2020-07-10  expires: never       usage: S
[ultimate] (1)* Chris (I'm free man) <chris@lesscrowds.org>

Please decide how far you trust this user to correctly verify other users' keys
(by looking at passports, checking fingerprints from different sources, etc.)

  1 = I don't know or won't say
  2 = I do NOT trust
  3 = I trust marginally
  4 = I trust fully
  5 = I trust ultimately
  m = back to the main menu

Your decision? 5
Do you really want to set this key to ultimate trust? (y/N) y

pub  rsa4096/89CE9931267F4CE8
     created: 2020-07-10  expires: never       usage: C
     trust: ultimate      validity: ultimate
sub  rsa4096/1C8EDC693A7E0B8E
     created: 2020-07-10  expires: never       usage: A
sub  rsa4096/C4C43816516A9059
     created: 2020-07-10  expires: never       usage: E
sub  rsa4096/DE45A3E23F339456
     created: 2020-07-10  expires: never       usage: S
[ultimate] (1)* Chris (I'm free man) <chris@lesscrowds.org>

# save changes
gpg> save
```

### GPG encrypt text string with public key

```bash
echo "hello" | gpg --encrypt --recepient whoami@example.com --armor
```

### GPG encrypt files with public key

Encrypt a file to be sent to the intended recipient. Here is an example of encrypting a file named `message.txt`:

```bash
echo "hello" > message.txt
gpg --encrypt --recepient whoami@example.com --armor message.txt

# alternatively
gpg --encrypt -r whoami@example.com --armor message.txt
gpg --encrypt -r <key id> --armor message.txt
```

Note:

- `-r` is shorthand for `--recipient`. The encrypted file will be named `message.txt.gpg` and is ready to be sent via email or some alternative means of communication securely. The only person that can `decrypt` the file will be the holder of the private key of whoami@example.com.

- The recipient may be an `email` or a specific `key id`. GPG will recognize which is which, and the case of multiple keys, just specifying the `key id` instead of the email may be most convenient. Note that this is true for all options and not just the encrypt option.

### GPG encrypt text string with public key and signing

```bash
# alternatively
gpg --encrypt --sign -r whoami@example.com --armor message.txt
gpg --encrypt --sign -r <key id> --armor message.txt
```

## GPG Decryption

### GPG file decryption with private key

The GPGJ encrypted file can now ONLY be `decrypted` by the holder of the `private key`. To GPG decrypt the file with the private key, run the following command:

```bash
gpg --decrypt message.txt.gpg
```
