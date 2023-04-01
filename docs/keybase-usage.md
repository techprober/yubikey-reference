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
- [Install keybase cli](#install-keybase-cli)
- [Login to keybase](#login-to-keybase)
- [Import GPG Key-Pair (including the encrypted private key)](#import-gpg-key-pair-including-the-encrypted-private-key)
- [Purge all GPG keys from keybase keyring](#purge-all-gpg-keys-from-keybase-keyring)
- [Export PGP key-pair from keybase](#export-pgp-key-pair-from-keybase)
- [Update Key-Pair to Keybase](#update-key-pair-to-keybase)

## Keybase Usage

### Install keybase cli

```bash
yay -S keybase-bin
```

### Login to keybase

```bash
keybase login
```

### Import GPG Key-Pair (including the encrypted private key)

```bash
# Assume you have already backuped your master private key, if not run
gpg --export-secret-key --armor <key id> > <dir to save the key>

# Import the master key-pair to keybase
keybase pgp import --import-file > <dir where the key is stored> --push-secret
```

### Purge all GPG keys from keybase keyring

Make sure your have saved your private key in a safe place

```bash
keybase pgp purge -p
```

### Export PGP Key-Pair from Keybase

```bash
# export public key
keybase pgp export > <dir>

# export private key
keybase pgp export --secret > <dir>
```

### Update Key-Pair to Keybase

```bash
keybase pgp update
```
