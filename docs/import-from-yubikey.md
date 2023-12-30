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
    <a href="https://www.yubico.com/blog/yubikey-firmware-update-yubikey-5-series-with-firmware-5-4/">
        <img src="https://img.shields.io/badge/yubikey--firmware-v5.4.3-brightgreen" alt="Firmware Version">
    </a>
</p>

## Introduction

CopyRight 2022-2024 TechProber. All rights reserved.

Maintainer: [ Kevin Yu (@yqlbu) ](https://github.com/yqlbu)

This repo serves to provide the end-users a way to speed up their Yubikey configuration process

## Table of Contents

<!-- vim-markdown-toc GFM -->

* [Import GPG keys from Yubikey](#import-gpg-keys-from-yubikey)
  * [Fetch stored information from yubikey](#fetch-stored-information-from-yubikey)
  * [fetch stored information from yubikey](#fetch-stored-information-from-yubikey-1)
  * [Trust the imported key](#trust-the-imported-key)
  * [Verify the result](#verify-the-result)

<!-- vim-markdown-toc -->

## Import GPG keys from Yubikey

> [!IMPORTANT]
> ONLY applicable when keys have been imported to Yubikey but not available in local machine

### Fetch stored information from yubikey

```bash
# fetch stored information from yubikey
gpg --edit-card
gpg/card> fetch
gpg -k
```

### fetch stored information from yubikey

```bash
gpg --edit-card
gpg/card> fetch
gpg -k
```

### Trust the imported key

```bash
gpg --edit-key <key id>
gpg> trust

Please decide how far you trust this user to correctly verify other users' keys
(by looking at passports, checking fingerprints from different sources, etc.)

  1 = I don't know or won't say
  2 = I do NOT trust
  3 = I trust marginally
  4 = I trust fully
  5 = I trust ultimately
  m = back to the main menu

Your decision? 5

gpg> quit
```

### Verify the result

```bash
# check if encryption and decryption
echo test | gpg --encrypt --recipient <your login email> | gpg --decrypt

# check ssh agent
ssh-add -L
```
