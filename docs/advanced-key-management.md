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

<!-- vim-markdown-toc GFM -->

* [Add new uid (user id) to an existing key-pair](#add-new-uid-user-id-to-an-existing-key-pair)
* [Export and import keys with GPG](#export-and-import-keys-with-gpg)
* [GPG pull public key from keyserver](#gpg-pull-public-key-from-keyserver)
* [Export GPG public key to keyserver](#export-gpg-public-key-to-keyserver)
* [Add additional email (uid) to the key](#add-additional-email-uid-to-the-key)
* [Remove subkey](#remove-subkey)
* [Update PGP key on key servers](#update-pgp-key-on-key-servers)

<!-- vim-markdown-toc -->

## Add new uid (user id) to an existing key-pair

Reference: https://gist.github.com/lovejavaee/4b870ad7fbc735df5245bf996bbd9c11

To add another identity to the exisitng key-pair (use email as identity)

```bash
gpg --edit-key <key id>
gpg> adduid
gpg> uid <new uid number>
gpg> trust
gpg> 5
gpg> save
```

---

## Export and import keys with GPG

Reference: https://linuxhint.com/export-import-keys-with-gpg/

```bash
# list all key-pairs
gpg -k

# export public key with GPG
gpg --export --armor <key id> > gpg.pub # keyid based
gpg --export --armor <uid (email)> > gpg.pub # uid based
cat gpg.pub

# export private key with GPG
gpg --export-secret-key --armor <key id> > gpg.key # keyid based
gpg --export-secret-key --armor <uid (email)> > gpg.key # uid based
cat gpg.key

# import keys (works for both public and private keys) from local storage
gpg --import <key location>

# verify results
gpg --list-secret-keys --with-keygrip
```

---

## GPG pull public key from keyserver

Notes: the following command will pull the target public key (including fingerprint) from keyserver

```bash
gpg --recv-keys <key id>
```

---

## Export GPG public key to keyserver

```bash
gpg --send-keys <key id>

# with a specific keyserver (without verifying identity)
gpg --keyserver 'hkps://keys.openpgp.org' --send-keys <key id>
```

Reference: https://keys.openpgp.org/about/usage

To configure GnuPG to use `keys.openpgp.org` as keyserver, add this line to your `gpg.conf` file:

```sh
keyserver hkps://keys.openpgp.org
```

Uploading your key, which outputs a direct link to the verification page (identity verification required, recommended)

```bash
gpg --export your_address@example.net | curl -T - https://keys.openpgp.org
```

Refresh existing public keys

```bash
gpg --keyserver 'hkps://keys.openpgp.org' --refresh-keys
```

Check the public key from keyserver

https://keys.openpgp.org/

---

## Add additional email (uid) to the key

```bash
gpg --edit-key <key id>
gpg> adduid
gpg> save
```

## Remove subkey

Reference: https://superuser.com/questions/1132263/how-to-delete-a-subkey-on-linux-in-gnupg

```bash
gpg --expert --edit-key <keyid>
# select the key index
gpg > key 1
gpg > delkey
```

## Update PGP key on key servers

Reference: https://askubuntu.com/questions/481813/how-to-update-pgp-key-on-key-servers

```bash
gpg --edit-key <keyid>
gpg> <operation>
gpg> save
# upload the modified key to the key servers
gpg --keyserver 'hkps://keys.openpgp.org' --send-keys <keyid>
```
