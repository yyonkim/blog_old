---
layout: default
title: PKG - APT
parent: Linux
permalink: /docs/Linux
tags:
- linux
---

## apt 쿼리
---
> APT 쿼리는 apt-file 또는 apt-cache 유틸리티를 설치 후 사용해야 한다.
- `$> sudo apt-get install apt-file`
- `$> sudo apt-file update`

Search package files in repository (in their name && description)
`$> sudo apt-cache search <package name>`

Search installed and available package (in their name)
`$> sudo apt-cache search -n <package name>`

To display basic information about the apache2 package:
`$> sudo apt-cache show apache2`

To display detailed information about the **apache2** package:
`$> sudo apt-cache showpkg apache2`

List all dependent packages for **apache2**:
`$> sudo apt-cache depends apache2`

List all installed kernel-related packages, and list all installed or available ones
`$> sudo apt-cache search "kernel"`
`$> sudo apt-cache search -n "kernel"` ---> 패키지 이름에 명시
`$> sudo apt-cache pkgnames "kernel"`

List all files in the **apache2** package:
`$> sudo apt-file list apache2`

Search the repository for a file named apache2.conf:
`$> sudo apt-file search apache2.conf`

`apt-file find <find path>` can be used to find which package provides the file specified as argument

Used to install new packages or update a package which is already installed: 
`$ sudo apt install [package]`

Used to remove a package from the system. This does not remove the configuration files: 
`$ sudo apt remove [package]`

Used to remove a package and its configuration files from a system: 
`$ sudo apt --purge remove [package] `

Command gets rid of any packages not needed anymore, such as older Linux kernel versions
`$> sudo apt autoremove`

This command cleans out cache files and any archived package files that have been installed by APT
`$> sudo apt clean`

## apt와 apt-get의 차이
---
> apt와 apt-get의 사용 상의 차이는 없으나 apt-get이 스크립트 내에서 참조되는 경우가 많기 때문에 더 잘 동작한다

## apt upgrade / apt update / apt dist-upgrade
---
> **apt update: 업데이트할 패키지들의 목록을 최신으로 갱신하는 명령어로, 실제 업데이트가 아니다. 따라서 Argument로 패키지 명을 줄 수 없음
> **apt upgrade: 의존성 체크를 하지 않고 강제 업데이트를 진행**
> **apt dist-upgrade: 의존성 체크를 하면서 upgrade에서 수행되지 못한 dependency까지 설치

## Remove와 Purge Remove의 차이
---
- remove: 설정 파일은 제외하고 패키지만 제거
- purge 또는 purge remove: 설정 파일까지 모두 제거


## Managing Groups of Packages with APT
---
> 리포지토리에서 다운로드 가능한 패키지 그룹 조회 후 단일 패키지만 설치할 경우

쿼리
`$> sudo apt-cache search <package name>`
```bash

bacula - network backup service - metapackage
bacula-client - network backup service - client metapackage
bacula-server - network backup service - server metapackage
cloud-utils - metapackage for installation of upstream cloud-utils source
compiz - OpenGL window and compositing manager
emacs - GNU Emacs editor (metapackag)
...

```

단일 패키지 인스톨
`$> sudo apt install bacula-client`
