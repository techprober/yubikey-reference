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

CopyRight 2022 TechProber. All rights reserved.

Maintainer: [ Kevin Yu (@yqlbu) ](https://github.com/yqlbu)

This repo serves to provide the end-users a way to speed up their Yubikey configuration process

[age-plugin-yubikey](https://github.com/str4d/age-plugin-yubikey) is a YubiKey plugin for age

## Table of Contents

<!-- vim-markdown-toc GFM -->

* [Reference](#reference)
* [Setup](#setup)
* [List existing keys from Yubikey](#list-existing-keys-from-yubikey)
* [Export key from Yubikey PIV slot](#export-key-from-yubikey-piv-slot)
* [Use with sops](#use-with-sops)
  * [Generate a new age key pairs from the identity file](#generate-a-new-age-key-pairs-from-the-identity-file)
* [Backup the new age private key](#backup-the-new-age-private-key)
  * [Test encryption](#test-encryption)

<!-- vim-markdown-toc -->

## Reference

- https://github.com/str4d/age-plugin-yubikey
- https://github.com/getsops/sops/issues/1103#issuecomment-1866390434

## Setup

```bash
# install
sudo pacman -S age-plugin-yubikey
# configure
age-plugin-yubikey
```

## List existing keys from Yubikey

```bash
age-plugin-yubikey --list
```

## Export key from Yubikey PIV slot

```bash
age-plugin-yubikey --identity --slot 1 > age-yubikey-identity.txt
```

## Use with sops

### Generate a new age key pairs from the identity file

```bash
age-keygen | age -e -r age1yubikey1q0p7guhr6dg2d56y66yxkzmwy98wxxcfuak7dt36ndqz95ld4tvr6fuah4n -a -o age-yubikey-master.enc.key
# output: Public key: age1uyvqk9kqjenmnwn08szr87cp6vgrr6dltt8cgardhhd5x0qfjcxqtdjucz
# write public key to a age-yubikey-master.pub
echo "age1uyvqk9kqjenmnwn08szr87cp6vgrr6dltt8cgardhhd5x0qfjcxqtdjucz" > age-yubikey-master.pub
```

## Backup the new age private key

```bash
age -i ./age-yubikey-identity.txt -d age-yubikey-master.enc.key > age-yubikey-master.key
```

### Test encryption

```bash
echo "hello" > foo.txt
SOPS_AGE_RECIPIENTS=age1uyvqk9kqjenmnwn08szr87cp6vgrr6dltt8cgardhhd5x0qfjcxqtdjucz sops -e foo.txt > foo.enc.txt
SOPS_AGE_KEY=$(age -i age-yubikey-identity.txt -d age-yubikey-master.enc.key) sops -d foo.enc.txt
# alternatively
SOPS_AGE_KEY_FILE=age-yubikey-master.key sops -d foo.enc.txt
```
