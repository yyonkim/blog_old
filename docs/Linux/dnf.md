---
layout: default
title: DNF
parent: Package Management
grand_parent: Linux
permalink: /docs/Linux/Foundations/Package_Management/dnf
---

# dnf & Yum
---
> yum 은 RHEL/CentOS7 -> 8 트랜지션 과정에서 dnf로 대체 되었다: 참조링크[1]

## Queries
---

```
## To search for a package with keyword in it, run:

$ dnf search keyword

## To display information about a package, run:

$ dnf info package-name

## To list packages installed, available, or updates, run:

$ dnf list [installed | updates | available ]

## To show information about package groups installed, available and updates, run:

$ dnf grouplist

## To show information about a package group:

$ dnf groupinfo packagegroup

## To show the owner of the package for file:

$ dnf provides /path/to/file

Please note the need to use at least one / in the file name, which can be confusing.
```

## Installing/Removing/Upgrading Packages
---
> 패키지 업데이트 과정에서 기존 configuration 파일의 변경사항이 발생하는 경우, 기존 파일은 .rpmsave 확장자와 함께 파일명이 변경된다.

```
## Install a package from a repository; also resolve and install dependencies:

$ sudo dnf install package

## Install a package from a local rpm file:

$ sudo dnf localinstall package-file

## Install a specific software group from a repository; also resolve and install dependencies for each package in the group:

$ sudo dnf groupinstall 'group-name'

## Remove a package from the system:

$ sudo dnf remove package

## Update a package from a repository (if no package is listed, update all packages):

$ sudo dnf update [package]
```

- 업데이트 관련 쿼리는 아래 3개이며 첫 번째 쿼리만 실제로 패키지를 업데이트한다
`$ sudo dnf update`
`$ sudo dnf check-updates`
`$ sudo dnf list updates`

## Additional dnf Commands
---
```
## Lists additional dnf plugins:

$ sudo dnf list "dnf-plugin*"

## Shows a list of enabled repositories:

$ sudo dnf repolist

## Provides an interactive shell in which to run multiple dnf commands (the second form executes the commands in file.txt):

$ sudo dnf shell
$ sudo dnf shell file.txt

## Downloads the packages for you (it stores them in the /var/cache/dnf directory):

$ sudo dnf install --downloadonly package

## Views the history of dnf commands on the system, and with the correction options, even undoes or redoes previous commands:

$ sudo dnf history

## Cleans up locally stored files and metadata under /var/cache/dnf. This saves space and does house cleaning of obsolete data:

$ sudo dnf clean [packages|metadata|expire-cache|rpmdb|plugins|all]
```

References:
[1] DNF, the next-generation replacement for YUM
