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

## Table of Contents

- [Home Page](https://github.com/TechProber/yubikey-reference)
- [Useful GnuPG Commands](#useful-gnupg-commands)

## Useful GnuPG Commands

Some useful commands that you may need

### Check current card status

```bash
gpg --card-status
```

### List existing gpg master key

```bash
# list existing gpg master key (short mode)
gpg -K

# list existing gpg master key (detail mode)
gpg -k
```

### List GPG key-pairs in different form

```bash
# public keys
gpg --list-keys --keyid-format=long
gpg --list-keys --keyid-format=short
# private keys
gpg --list-secret-keys --keyid-format=long
gpg --list-secret-keys --keyid-format=short
```

### Print the GPG public key, in ASCII (base64) format

```bash
gpg --armor --export <key id>
```

### Delete an existing keys

```bash
# delete an existing master private key and all its sub-keys
gpg --delete-secret-key <key id>

# delete an existing GPG public key
gpg --delete-key <key id>
```

### Backup PGP key

```bash
# backup PGP private key
gpg --export-secret-key --armor <key id> > <dir to save the key>

# backup PGP public key
gpg --export --armor <key id> > <dir to save the key>
```

### Fetch stored information from yubikey

```bash
# fetch stored information from yubikey
gpg --edit-card
gpg/card> fetch
gpg -k
```

### List all keys

```bash
# list all public keys
gpg --list-keys
gpg -k

# list all private keys
gpg --list-secret-keys
```
