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

- [Reference](#reference)
- [Dependencies](#dependencies)
- [Management Tools](#management-tools)
- [Authentication Tools](#authentication-tools)
- [Useful GnuPG Commands](#useful-gnupg-commands)
- [Setup](#setup)
- [Multiple Keys](#multiple-keys)
- [Keybase Usage](#keybase-usage)

### References

- [YubiKey Archlinux Wiki](https://wiki.archlinux.org/title/YubiKey)
- [YubiKey-Guide](https://github.com/drduh/YubiKey-Guide)
- [Using Your YubiKey with OpenPGP](https://support.yubico.com/hc/en-us/articles/360013790259-Using-Your-YubiKey-with-OpenPGP)
- [How to use a GPG key for SSH authentication](https://www.linode.com/docs/guides/gpg-key-for-ssh-authentication/)
- [Yubikey GnuPG + SSH Agent with Fish](https://www.foxk.it/blog/gpg-ssh-agent-fish/)
- [Yubico Authenticator](https://www.yubico.com/products/yubico-authenticator/)
- [How To: Programming the YubiKey with a YubiOTP Credential](https://www.youtube.com/watch?v=nSDSqJiwono)
- [Yubico OTP key upload](https://upload.yubico.com/)

---

### Dependencies

```bash
sudo pacman -S opensc usbutils gpg pinentry
```

---

### Management Tools

#### YubiKey Manager

Python library and command-line tool (ykman) for configuring and querying a YubiKey over USB

```bash
sudo pacman -S yubikey-manager
sudo systemctl enable pcscd --now

# check key status
ykman info
```

---

### Authentication Tools

#### Yubico PAM

PAM user authentication with either Yubico OTP or challenge-response

```bash
sudo pacman -S yubico-pam
```

#### Yubico PAM-U2F

PAM user authentication with U2F

```bash
sudo pacman -S pam-u2f
```

#### libfido2

Client-side U2F support. Enables web browsers to use the U2F protocol for authentication with your YubiKey

```bash
sudo pacman -S libfido2
```

---

## Useful GnuPG Commands

Some useful commands that you may need

```bash
# check current card status
gpg --card-status

# list existing gpg master key
gpg -K

# list existing gpg master key (detail mode)
gpg -k

# list the long form of the GPG key-pairs
gpg --list-secret-keys --keyid-format=long

# prints the GPG pubkey key, in ASCII armor format
gpg --armor --export <key id>

# delete an existing master private key and all its sub-keys
gpg --delete-secret-key <key id>

# delete an existing GPG public key
gpg --delete-key <key id>

# backup your GPG private key
gpg --export-secret-key --armor <key id> > <dir to save the key>

# fetch stored information from yubikey
gpg --edit-card
gpg/card> fetch
gpg -K
```

---

## Setup

### Step [0] - Basic Card Setup

Change PIN

Reference: https://github.com/drduh/YubiKey-Guide#change-pin

```bash
gpg --card-edit
gpg/card> admin
Admin commands are allowed

gpg/card> passwd
```

Turn on KDF Settings

Reference: https://github.com/drduh/YubiKey-Guide#enable-kdf

```bash
gpg --card-edit
gpg/card> admin
Admin commands are allowed

gpg/card> kdf-setup
gpg/card> list
```

Set information

Reference: https://github.com/drduh/YubiKey-Guide#set-information

```bash
gpg --card-edit
gpg/card> admin
Admin commands are allowed

gpg/card> name
gpg/card> login (email)
gpg/card> lang
gpg/card> list
```

### Step [1] - Follow the guide to setup GPG key with YubiKey

Guide: https://support.yubico.com/hc/en-us/articles/360013790259-Using-Your-YubiKey-with-OpenPGP

Notes: steps including import the GPG private key to your YubiKey

You should have a similar output as the following

<details><summary>card-info</summary>
  
</br>

```
# gpg --card-status

Reader ...........: Yubico YubiKey OTP FIDO CCID 00 00
Application ID ...: D2760001240103040006181009610000
Application type .: OpenPGP
Version ..........: 3.4
Manufacturer .....: Yubico
Serial number ....: 18100961
Name of cardholder: Kevin YU
Language prefs ...: en
Salutation .......:
URL of public key : [not set]
Login data .......: kevinyu211@yahoo.com
Signature PIN ....: not forced
Key attributes ...: rsa4096 rsa4096 rsa4096
Max. PIN lengths .: 127 127 127
PIN retry counter : 5 3 5
Signature counter : 0
KDF setting ......: on
Signature key ....: B08F E264 4F9F C450 AF42  5FF5 B94F 394B 7288 4017
      created ....: 2022-04-15 09:49:28
Encryption key....: 2F73 DEF6 57B5 33A3 45BB  3320 356C 925E ABB2 A874
      created ....: 2022-04-15 09:49:28
Authentication key: 9E3C D061 5BBB 5607 49A6  6170 73E4 7E44 AA59 638E
      created ....: 2022-04-15 09:49:58
General key info..: pub  rsa4096/B94F394B72884017 2022-04-15 Kevin YU (kevin as default) <kevinyu211@yahoo.com>
sec>  rsa4096/B94F394B72884017  created: 2022-04-15  expires: never
                                card-no: 0006 18100961
ssb>  rsa4096/356C925EABB2A874  created: 2022-04-15  expires: never
                                card-no: 0006 18100961
ssb>  rsa4096/73E47E44AA59638E  created: 2022-04-15  expires: never
                                card-no: 0006 18100961
ssb   rsa4096/99C3D85A28AA5C7C  created: 2022-04-15  expires: never
```

</details>

Test configuration

```bash
echo test | gpg --encrypt --recipient <your login email> | gpg --decrypt
```

### Step [2] - SSH Configuration

References:

- https://github.com/drduh/YubiKey-Guide
- https://www.linode.com/docs/guides/gpg-key-for-ssh-authentication/

Create gpg-agent configuration

```bash
cat <<EOF >> ~/.gnupg/gpg-agent.conf
# https://github.com/drduh/config/blob/master/gpg-agent.conf
enable-ssh-support
default-cache-ttl 60
max-cache-ttl 120
gpg-connect-agent reloadagent /bye
pinentry-program /usr/bin/pinentry-curses
EOF
```

Replace agents (Important)

Reference: https://github.com/drduh/YubiKey-Guide#replace-agents

You may find the configuration that fits your current shell environment from the link above

I am using fish, so adding the following lines to `~/.config/fish/config.fish` should be good to go.

```fish
# Ensure that GPG Agent is used as the SSH agent
set -e SSH_AUTH_SOCK
set -U -x SSH_AUTH_SOCK (gpgconf --list-dirs agent-ssh-socket)
set -x GPG_TTY (tty)
gpgconf --launch gpg-agent
```

Relaunch your shell, and enjoy!

### Step [3] - Verify your key

If you follow the steps above properly, you should be able to see the newly generated key with the following command

```bash
# ssh-add -L
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC4YpotphfZittW6FZDI0phTUra708NiCay08aK3Nm3asNXF+OGggUljDcXZDR9yiVj7CdPqq67qH1ce1AaALz5KmT8j91InyHf4Rd+doy0MaLxl3lFqAma5cXU6UXXdScN3xlUE92NXNPsZiEVT5K6QVXoz0qGdEb1ptxKMou3zw6mat04KOpXfTNwJQEapSjpq6yDNamjmRyiOFZbXeTiqAYihXv9sfCbtdWgBryK7lqr7IkByT2wqaEmxXQg+eQoZuN8gFgxWxVNTE62KsM+9JIz6gvEB37x3pdJeip260yusNez0rzSTts+l2SO2+nNn3Fc18RHFGyvbqB29K+Tt6/LhHIxcGwyQhgOQY3KAWugOUWNkWyqW8zEHM4a2p5K+VtBFin2cSBwN4HO4pxVYsHW3MxK3fg4iPB/LkyR+MvAPCLP1eVNP3zy7t5J7ZUZdoBcIeGP6Hl70jvhptJla0x5w1ixIthkj3qAikiXscLJ9Jm8MChEiVzRWGwJhOgoCsnsiN4udvCQ4oQUW3K3zVpKmHtOsbNNZKQGXQOB3zxBd7qHNj+6ktL9/WdxWXWMkfG9mB1nS/+wNl0vSOhV9WxCzXQh8QzmiDcX+rEuMXjz4DD2QZ0bgOFSBwaJ9wyVUqylRj9B1EgjiL1ouU6fY/FPJDXbOhPV+XQ7viSAlQ== cardno:18 100 961
```

### Step [4] - Install your PGP-Yubikey to remote servers

Save gpg-yubikey to `~/.ssh/id_rsa_yubikey.pub`

```bash
$ ssh-add -L | grep "cardno:" > ~/.ssh/id_rsa_yubikey.pub
```

Use `ansible playbook` to install the `id_rsa_yubikey.pub` to remote servers

Please find details at https://github.com/TechProber/cloud-estate/discussions/100

---

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

---
