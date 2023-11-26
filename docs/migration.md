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
<!-- vim-markdown-toc GFM -->

* [Migration](#migration)
  * [Backup existing master key](#backup-existing-master-key)
  * [Fetch keys](#fetch-keys)
  * [Verify if keys have been imported](#verify-if-keys-have-been-imported)
  * [Set ultimate trust](#set-ultimate-trust)
  * [Verify the result](#verify-the-result)

<!-- vim-markdown-toc -->

## Migration

To setup yubikey on another host after fresh installation, follow the steps below

Reference: <https://github.com/drduh/YubiKey-Guide#multiple-hosts>

### Backup existing master key

```bash
# list existing master key, and get the key id
gpg -K

mkdir -p ~/.local/creds
gpg --export-secret-key --armor <key id> > ~/.local/creds/GPG_key.backup
```

---

### Fetch keys

```bash
gpg --edit-card

gpg/card> fetch
gpg: requesting key from 'hkps://keyserver.ubuntu.com:443/pks/lookup?op=get&search=0xFF3E7D88647EBCDB'
gpg: /home/pi/.gnupg/trustdb.gpg: trustdb created
gpg: key FF3E7D88647EBCDB: public key "Dr Duh <doc@duh.to>" imported
gpg: Total number processed: 1
gpg:               imported: 1

gpg/card> quit
```

This step also imports the private key stubs from the Yubikey.

### Verify if keys have been imported

```bash
gpg -k
gpg -K
```

### Set ultimate trust

```bash
gpg --edit-key $KEYID
gpg> trust
Your decision? 5
Do you really want to set this key to ultimate trust? (y/N) y
gpg> quit
```

### Verify the result

```bash
# check if encryption and decryption
echo test | gpg --encrypt --recipient <your login email> | gpg --decrypt

# check ssh agent
ssh-add -L
```
