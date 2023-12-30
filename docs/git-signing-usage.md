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

* [Git Signing Usage](#git-signing-usage)

<!-- vim-markdown-toc -->

## Git Signing Usage

Reference: https://gist.github.com/alopresto/b8d940197b4c314e27188a6852198d2d

Upload your key to Github followed by the [guide](https://help.github.com/articles/adding-a-new-gpg-key-to-your-github-account/)

```bash
# enable signing for every git commit
git config --global commit.gpgSign true
# enable signing for tag
git config --global tag.forceSignAnnotated true
# reference the keyid that is used for signing
git config --global user.signingkey <keyid>

# test result
echo "hello" > test
git add test
git commit -S "this commit is signed by a GPG key"
git logs
```
