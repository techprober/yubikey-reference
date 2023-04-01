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
- [Dependencies](#Dependencies)
- [Management Tools](#management-tools)
- [Authentication Tools](#authentication-tools)
- [Create Softlinks](#create-softlinks)

## Installation

### Dependencies

```bash
# archlinux
sudo pacman -Sy opensc usbutils pcsclite ccid gnupg pinentry libusb-compat

# fedora
sudo pacman -Sy opensc usbutils pcsc-lite ccid gnupg pinentry
```

**Note:**

- To allow `scdaemon` direct access to USB smartcard readers the optional dependency `libusb-compat` must be installed
- `pcscd (pcsclite)` is a daemon which handles access to smartcard (SCard API)

---

### Management Tools

#### YubiKey Manager

Python library and command-line tool (ykman) for configuring and querying a YubiKey over USB

```bash
# archlinux
sudo pacman -Sy yubikey-manager

# fedora
sudo dnf install yubikey-manager.noarch

# fedora gui client
sudo dnf install yubikey-manager-qt.x86_64 yubioath-desktop.x86_64

sudo systemctl enable pcscd --now

# check key status
ykman info
```

---

### Authentication Tools

#### Yubico PAM

PAM user authentication with either Yubico OTP or challenge-response

```bash
sudo pacman -Sy yubico-pam
```

#### Yubico PAM-U2F

PAM user authentication with U2F

```bash
sudo pacman -Sy pam-u2f
```

#### libfido2

Client-side U2F support. Enables web browsers to use the U2F protocol for authentication with your YubiKey

```bash
sudo pacman -Sy libfido2
```

---

### Create Softlinks

scdaemon (smartcard daemon for the GnuPG system)

```bash
sudo ln -s /usr/lib/gnupg/scdaemon /usr/bin/scdaemon
```

---

### Configure scdaemon

```bash
# ~/.gnupg/scdaemon.conf
debug-level basic
log-file /var/log/scdaemon.log
reader-port Yubico Yubi
```
