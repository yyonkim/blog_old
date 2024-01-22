---
layout: default
title: PKG - DPKG
parent: Linux
permalink: /docs/Linux
tags:
- linux
---

# dpkg - Queries
---

Lists all packages installed on the system
`$> dpkg -l`

Lists files included in the installed package "A"
`$> dpkg -L A`

Verify installed package's integrity?
`$> dpkg -V <package name>`

Show package information
`$> dpkg -s <package name>`

Show what package owns /etc/logrotate.conf file
`$> dpkg -S /etc/logrotate.conf`

Remove package except configurations file
`$> dpkg -r <package name>`

Remove package including configurations file
`$> dpkg -P <package name>`

Install or upgrade package
`$> dpkg -i <package name>`
