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
- [Backup existing master key](#backup-existing-master-key)
- [Switch between two or more Yubikeys](#switch-between-two-or-more-yubikeys)
- [Delete the original key](#delete-the-original-key)
- [Import the master key from backup](#import-the-master-key-from-backup)
- [Import the master key to the new YubiKey](#import-the-master-key-to-the-new-yubikey)
- [Trust the imported key](#trust-the-imported-key)
- [Verify the result](#verify-the-result)

## Multiple Keys

Reference: https://github.com/drduh/YubiKey-Guide#multiple-yubikeys

To import the existing key to `another` YubiKey, follow the steps below

### Backup existing master key

```bash
# list existing master key, and get the key id
gpg -K

mkdir -p ~/.local/creds
gpg --export-secret-key --armor <key id> > ~/.local/creds/GPG_key.backup
```

---

### Switch between two or more Yubikeys

Reference: https://github.com/drduh/YubiKey-Guide#switching-between-two-or-more-yubikeys

```bash
# Insert the first Yubikey (which has a different serial number) and run the following command
gpg-connect-agent "scd serialno" "learn --force" /bye
```

Notes:

- GPG will then scan your first Yubikey for GPG keys and recreate the stubs to point to the GPG keyID and Yubikey Serial number of this first Yubikey
- To return to using the second Yubikey just repeat (insert other Yubikey and re-run command).
- Obviously this command is not easy to remember so it is recommended to either create a script or a shell alias to make this more user friendly.

---

### Delete the original key

Delete the original master key

```bash
# list existing master key, and get the key id
gpg -K

# delete the existing key
gpg --delete-secret-key <key id>
gpg --delete-key <key id>
```

---

### Import the master key from backup

```bash
gpg --import ~/.local/creds/GPG_key.backup

# verify the result
gpg -K
gpg -k
```

---

### Import the master key to the new YubiKey

```bash
gpg --edit-key <key id>
```

Follow the procedures in https://support.yubico.com/hc/en-us/articles/360013790259-Using-Your-YubiKey-with-OpenPGP

---

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

---

### Verify the result

```bash
# check if encryption and decryption
echo test | gpg --encrypt --recipient <your login email> | gpg --decrypt

# check ssh agent
ssh-add -L
```
