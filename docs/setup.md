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

## Table of Contents

- [Home Page](https://github.com/TechProber/yubikey-reference)
<!-- vim-markdown-toc GFM -->

* [Setup](#setup)
  * [Basic Card Setup](#basic-card-setup)
    * [Change PIN](#change-pin)
    * [Turn on KDF Settings](#turn-on-kdf-settings)
    * [Set information](#set-information)
  * [Setup GPG key with YubiKey](#setup-gpg-key-with-yubikey)
    * [Test configuration](#test-configuration)
  * [SSH Configuration](#ssh-configuration)
    * [Create gpg-agent configuration](#create-gpg-agent-configuration)
    * [Replace agents (\*Important)](#replace-agents-important)
    * [Configure pinentry to use the correct TTY (Important)](#configure-pinentry-to-use-the-correct-tty-important)
    * [Change to GUI Pinentry (Optional)](#change-to-gui-pinentry-optional)
    * [Unattended passphrase (\*Important)](#unattended-passphrase-important)
  * [Verify key](#verify-key)
  * [Install PGP-Yubikey to remote servers](#install-pgp-yubikey-to-remote-servers)
  * [Remove SSH Key from GPG Agent](#remove-ssh-key-from-gpg-agent)
  * [Restart GPG Agent](#restart-gpg-agent)
  * [Restart scdaemon](#restart-scdaemon)
  * [Unlock card](#unlock-card)
  * [Add to crontab](#add-to-crontab)
  * [Check current gpgconf](#check-current-gpgconf)

<!-- vim-markdown-toc -->

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
# https://www.gnupg.org/documentation/manuals/gnupg/Agent-Options.html
enable-ssh-support
default-cache-ttl 60
max-cache-ttl 120
ttyname $GPG_TTY
allow-loopback-pinentry
pinentry-program /usr/bin/pinentry-curses
EOF
```

#### Replace agents (\*Important)

Reference: https://github.com/drduh/YubiKey-Guide#replace-agents

You may find the configuration that fits your current shell environment from the link above

I am using fish, so adding the following lines to `~/.config/fish/config.fish` should be good to go.

```fish
# References: https://github.com/drduh/YubiKey-Guide/issues/89#issuecomment-1501886324
# Ensure that GPG Agent is used as the SSH agent
set -x KEY_SERIAL (gpg-connect-agent 'scd serialno' /bye | head -n 1 | cut -f3 -d ' ')
set -x GNUPGHOME "$HOME/.gnupg"
set -x PINENTRY_USER_DATA "USE_CURSES=1"
set -x SSH_AUTH_SOCK (gpgconf --list-dirs agent-ssh-socket)
set -x GPG_TTY (tty)
gpgconf --launch gpg-agent
echo "UPDATESTARTUPTTY" | gpg-connect-agent > /dev/null 2>&1
```

for `zsh` or `bash`, the equivalent setup will be as the following:

```bash
# References: https://github.com/drduh/YubiKey-Guide/issues/89#issuecomment-1501886324
# Ensure that GPG Agent is used as the SSH agent
export KEY_SERIAL=$(gpg-connect-agent 'scd serialno' /bye | head -n 1 | cut -f3 -d ' ')
export GNUPGHOME="$HOME/.gnupg"
export PINENTRY_USER_DATA="USE_CURSES=1"
export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)
export GPG_TTY=$(tty)
gpgconf --launch gpg-agent
echo "UPDATESTARTUPTTY" | gpg-connect-agent > /dev/null 2>&1
```

#### Configure pinentry to use the correct TTY (Important)

Reference: https://wiki.archlinux.org/title/GnuPG#Configure_pinentry_to_use_the_correct_TTY

`gpg-connect-agent updatestartuptty /bye >/dev/null` will reset the tty and prompt your to type a pin for authentication to continue the ssh connection

Reload GPG Agent to use tty section

```bash
# $HOME/.gnupg/gpg-agent.conf
...
pinentry-program /usr/bin/pinentry-curses

gpg-connect-agent reloadagent /bye
```

#### Change to GUI Pinentry (Optional)

Reference: https://stackoverflow.com/a/66209785

```bash
# install pinentry-gtk2
sudo pacman -S pinentry-gtk-2
# configure gpg-agent
# $HOME/.gnupg/gpg-agent.conf
pinentry-program /usr/bin/pinentry-gtk-2
# reload gpg-agent
pkill gpg-agent
gpg-connect-agent reloadagent /bye
```

#### Unattended passphrase (\*Important)

Reference: https://wiki.archlinux.org/title/GnuPG#Unattended_passphrase

Starting with GnuPG 2.1.0 the use of gpg-agent and pinentry is required, which may break backwards compatibility for passphrases piped in from STDIN using the --passphrase-fd 0 commandline option

```bash
#~/.gnupg/gpg-agent.conf

allow-loopback-pinentry
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
ssh-add -L | grep "cardno:" > ~/.ssh/id_rsa_yubikey.pub
```

Use `ansible playbook` to install the `id_rsa_yubikey.pub` to remote servers

Please find details at https://github.com/TechProber/cloud-estate/discussions/100

---

### Remove SSH Key from GPG Agent

> **Note** `ssh -d` is broken with gpg's agent. Use the following workaround the remove the key instead:

Run the `gpg-connect-agent` command from the commandline to connect to the agent. Then, from the prompts there, enter this command to list the ssh keys

```
KEYINFO --ssh-list --ssh-fpr
```

You should see something like:

```
S KEYINFO 3365433C34421CC53B52C9A82169FD2328CF610B D - - - P df:a2:36:8d:ad:88:b3:cc:00:96:10:d4:c9:2c:e0:df - S
OK
```

Now, to remove the from the agent:

```bash
DELETE_KEY 3365433C34421CC53B52C9A82169FD2328CF610B
```

---

### Restart GPG Agent

In some cases, you might need to restart the GPG agent. To do so, use the following command:

```bash
# kill existing gpg agent
gpgconf --kill gpg-agent
# reload agent
gpg-connect-agent reloadagent /bye
# reconnect agent with the input key
gpg-connect-agent "scd serialno" "learn --force" /bye
```

---

### Restart scdaemon

Reference: https://man.archlinux.org/man/scdaemon.1.en

By default, the scdaemon will not be restarted automatically, so you need to have a script with cron settings to reset the gpg-agent auth window(session). To do so, follow the guide:

```bash
gpgconf --reload scdaemon
```

### Unlock card

Reference: https://github.com/drduh/YubiKey-Guide/issues/89

You might encounter issue that inserting the Yubikey and attempting to ssh does not trigger gpg-agent to prompt me for a pin. Even with export GPG_TTY=$(tty) in your shells startup files, `gpg` still does not know where to display the pin-entry. There is a workaround to trigger an unlock-card action with the following commands:

```bash
export SERIAL=$(gpg-connect-agent 'scd serialno' /bye | head -n 1 | cut -f3 -d' ')
gpg-connect-agent "scd checkpin $SERIAL" /bye
```

With the above workarounds, the ssh functionality will work again afterward.

You may also add the following alias to easily trigger the `unlock-card` action:

```bash
# zsh
alias unlock-card="gpg-connect-agent 'scd checkpin $SERIAL' /bye"
# fish
abbr unlock-card "gpg-connect-agent 'scd checkpin $SERIAL' /bye"
```

### Add to crontab

```bash
sudo pacman -S cronie

# /etc/crontab

# run every 20 minutes to restart scdaemon
*/20 * * * * kev /usr/bin/gpgconf --reload scdaemon
```

With the above setup, the gpg-agent will reset its auth window(session) every 20 minutes.

### Check current gpgconf

```bash
gpgconf

# outputs
gpg:OpenPGP:/usr/bin/gpg2
gpgsm:S/MIME:/usr/bin/gpgsm
keyboxd:Public Keys:/usr/libexec/keyboxd
gpg-agent:Private Keys:/usr/bin/gpg-agent
scdaemon:Smartcards:/usr/libexec/scdaemon
tpm2daemon:TPM:/usr/libexec/tpm2daemon
dirmngr:Network:/usr/bin/dirmngr
pinentry:Passphrase Entry:/usr/bin/pinentry
```
