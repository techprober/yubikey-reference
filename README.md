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
- [Reference](#reference)
- [Installation](https://github.com/TechProber/yubikey-reference/blob/master/docs/installation.md)
- [Useful GnuPG Commands](https://github.com/TechProber/yubikey-reference/blob/master/docs/useful-gnupg-commands.md)
- [Setup](https://github.com/TechProber/yubikey-reference/blob/master/docs/setup.md)
- [Advanced Key Management](https://github.com/TechProber/yubikey-reference/blob/master/docs/advanced-key-management.md)
- [multiple Keys](https://github.com/TechProber/yubikey-reference/blob/master/docs/multitple-keys.md)
- [Keybase Usage](https://github.com/TechProber/yubikey-reference/blob/master/docs/keybase-usage.md)
- [Git Signing Usage](https://github.com/TechProber/yubikey-reference/blob/master/docs/git-signing-usage.md)

## References

- [YubiKey Archlinux Wiki](https://wiki.archlinux.org/title/YubiKey)
- [YubiKey-Guide](https://github.com/drduh/YubiKey-Guide)
- [Using Your YubiKey with OpenPGP](https://support.yubico.com/hc/en-us/articles/360013790259-Using-Your-YubiKey-with-OpenPGP)
- [How to use a GPG key for SSH authentication](https://www.linode.com/docs/guides/gpg-key-for-ssh-authentication/)
- [Yubikey GnuPG + SSH Agent with Fish](https://www.foxk.it/blog/gpg-ssh-agent-fish/)
- [Yubico Authenticator](https://www.yubico.com/products/yubico-authenticator/)
- [How To: Programming the YubiKey with a YubiOTP Credential](https://www.youtube.com/watch?v=nSDSqJiwono)
- [Yubico OTP key upload](https://upload.yubico.com/)
- [How to export and import keys with GPG](https://linuxhint.com/export-import-keys-with-gpg/)
- [GPG: Change email for key in PGP key servers](https://gist.github.com/lovejavaee/4b870ad7fbc735df5245bf996bbd9c11)

## Multiple Keys

Reference: https://github.com/drduh/YubiKey-Guide#multiple-yubikeys

To import the existing key to `another` YubiKey, follow the steps below

### Steps to reproduce

#### Step [0] - Backup your existing master key

```bash
# list existing master key, and get the key id
gpg -K

mkdir -p ~/.local/creds
gpg --export-secret-key --armor <key id> > ~/.local/creds/GPG_key.backup
```

#### Step [1] - Switch between two or more Yubikeys

Reference: https://github.com/drduh/YubiKey-Guide#switching-between-two-or-more-yubikeys

```bash
# Insert the first Yubikey (which has a different serial number) and run the following command
gpg-connect-agent "scd serialno" "learn --force" /bye
```

Notes:

- GPG will then scan your first Yubikey for GPG keys and recreate the stubs to point to the GPG keyID and Yubikey Serial number of this first Yubikey
- To return to using the second Yubikey just repeat (insert other Yubikey and re-run command).
- Obviously this command is not easy to remember so it is recommended to either create a script or a shell alias to make this more user friendly.

#### Step [2] - Delete the original key

Delete the original master key

```bash
# list existing master key, and get the key id
gpg -K

# delete the existing key
gpg --delete-secret-key <key id>
gpg --delete-key <key id>
```

#### Step [3] - Import the master key from backup

```bash
gpg --import ~/.local/creds/GPG_key.backup

# verify the result
gpg -K
gpg -k
```

#### Step [4] - Import the key to the new YubiKey

```bash
gpg --edit-key <key id>
```

Follow the procedures in https://support.yubico.com/hc/en-us/articles/360013790259-Using-Your-YubiKey-with-OpenPGP

#### Step [5] - Trust the imported key

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

#### Step [6] - Verify the result

```bash
# check if encryption and decryption
echo test | gpg --encrypt --recipient <your login email> | gpg --decrypt

# check ssh agent
ssh-add -L
```

---

## Keybase Usage

### Install Keybase CLI

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

### Purge all GPGkeys from Keybase Keyring

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

---

### Git Signing Usage

Reference: https://gist.github.com/alopresto/b8d940197b4c314e27188a6852198d2d

Upload your key to Github followed by the [guide](https://help.github.com/articles/adding-a-new-gpg-key-to-your-github-account/)

```bash
# enable signing for every git commit (optional)
git config --global commit.gpgsign true

# reference the keyid that is used for signing
git config --global user.signingkey <keyid>

# test result
echo "hello" > test
git add test
git commit -S "this commit is signed by a GPG key"
git logs
```

---
