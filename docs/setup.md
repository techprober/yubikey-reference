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
- [Basic Card Setup](#basic-card-setup)
- [Setup GPG key with YubiKey](#setup-gpg-key-with-yubikey)
- [SSH Configuration](ssh-configuration)
- [Verify key](#verify-key)
- [Install PGP-Yubikey to remote servers](#install-pgp-yubikey-to-remote-servers)

---

## Setup

### Basic Card Setup

#### Change PIN

Reference: https://github.com/drduh/YubiKey-Guide#change-pin

```bash
gpg --card-edit
gpg/card> admin
Admin commands are allowed

gpg/card> passwd
```

#### Turn on KDF Settings

Reference: https://github.com/drduh/YubiKey-Guide#enable-kdf

```bash
gpg --card-edit
gpg/card> admin
Admin commands are allowed

gpg/card> kdf-setup
gpg/card> list
```

#### Set information

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

---

### Setup GPG key with YubiKey

Guide: https://support.yubico.com/hc/en-us/articles/360013790259-Using-Your-YubiKey-with-OpenPGP
Notes:

- The above steps including import the `GPG private key` to your `YubiKey`

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

#### Test configuration

```bash
echo test | gpg --encrypt --recipient <your login email> | gpg --decrypt
```

---

### SSH Configuration

References:

- https://github.com/drduh/YubiKey-Guide
- https://www.linode.com/docs/guides/gpg-key-for-ssh-authentication/

#### Create gpg-agent configuration

```bash
cat <<EOF >> ~/.gnupg/gpg-agent.conf
# https://github.com/drduh/config/blob/master/gpg-agent.conf
enable-ssh-support
default-cache-ttl 60
max-cache-ttl 120
pinentry-mode loopback
pinentry-program /usr/bin/pinentry-curses
EOF
```

#### Replace agents (\*Important)

Reference: https://github.com/drduh/YubiKey-Guide#replace-agents

You may find the configuration that fits your current shell environment from the link above

I am using fish, so adding the following lines to `~/.config/fish/config.fish` should be good to go.

```fish
# Ensure that GPG Agent is used as the SSH agent
set -e SSH_AUTH_SOCK
set -U -x SSH_AUTH_SOCK (gpgconf --list-dirs agent-ssh-socket)
set -x GPG_TTY (tty)
gpg-connect-agent updatestartuptty /bye >/dev/null
```

#### Configure pinentry to use the correct TTY (\*Important)

Reference: https://wiki.archlinux.org/title/GnuPG#Configure_pinentry_to_use_the_correct_TTY

`gpg-connect-agent updatestartuptty /bye >/dev/null` will reset the tty and prompt your to type a pin for authentication to continue the ssh connection

```bash
#~/.gnupg/gpg.conf

gpg-connect-agent updatestartuptty /bye >/dev/null
```

#### Unattended passphrase (\*Important)

Reference: https://wiki.archlinux.org/title/GnuPG#Unattended_passphrase

Starting with GnuPG 2.1.0 the use of gpg-agent and pinentry is required, which may break backwards compatibility for passphrases piped in from STDIN using the --passphrase-fd 0 commandline option

```bash
#~/.gnupg/gpg-agent.conf

pinentry-mode loopback
```

---

### Verify key

If you follow the steps above properly, you should be able to see the newly generated key with the following command

```bash
# ssh-add -L
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC4YpotphfZittW6FZDI0phTUra708NiCay08aK3Nm3asNXF+OGggUljDcXZDR9yiVj7CdPqq67qH1ce1AaALz5KmT8j91InyHf4Rd+doy0MaLxl3lFqAma5cXU6UXXdScN3xlUE92NXNPsZiEVT5K6QVXoz0qGdEb1ptxKMou3zw6mat04KOpXfTNwJQEapSjpq6yDNamjmRyiOFZbXeTiqAYihXv9sfCbtdWgBryK7lqr7IkByT2wqaEmxXQg+eQoZuN8gFgxWxVNTE62KsM+9JIz6gvEB37x3pdJeip260yusNez0rzSTts+l2SO2+nNn3Fc18RHFGyvbqB29K+Tt6/LhHIxcGwyQhgOQY3KAWugOUWNkWyqW8zEHM4a2p5K+VtBFin2cSBwN4HO4pxVYsHW3MxK3fg4iPB/LkyR+MvAPCLP1eVNP3zy7t5J7ZUZdoBcIeGP6Hl70jvhptJla0x5w1ixIthkj3qAikiXscLJ9Jm8MChEiVzRWGwJhOgoCsnsiN4udvCQ4oQUW3K3zVpKmHtOsbNNZKQGXQOB3zxBd7qHNj+6ktL9/WdxWXWMkfG9mB1nS/+wNl0vSOhV9WxCzXQh8QzmiDcX+rEuMXjz4DD2QZ0bgOFSBwaJ9wyVUqylRj9B1EgjiL1ouU6fY/FPJDXbOhPV+XQ7viSAlQ== cardno:18 100 961
```

---

### Install PGP-Yubikey to remote servers

Save gpg-yubikey to `~/.ssh/id_rsa_yubikey.pub`

```bash
$ ssh-add -L | grep "cardno:" > ~/.ssh/id_rsa_yubikey.pub
```

Use `ansible playbook` to install the `id_rsa_yubikey.pub` to remote servers

Please find details at https://github.com/TechProber/cloud-estate/discussions/100
