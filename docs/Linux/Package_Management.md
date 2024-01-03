---
layout: default
title: Package Management
parent: Linux
has_children: true
permalink: /docs/Linux/Foundations/Package_Management/
---

## Package Types
---
> Binary packages are the ones that system administrators have to deal with most of the time.

- Binary Packages
	- Binary packages contain files ready for deployment, including executable files and libraries. These are architecture-dependent.
- Source Packages
	- Source packages are used to generate binary packages; one should always be able to rebuild a binary package from the source package. One source package can be used for multiple architectures.
- Architecture-Independent Packages
	- Architecture-independent packages contain files and scripts that run under script interpreters, as well as documentation and configuration files.
- Meta-Packages
	- Meta-packages are groups of associated packages that collect everything needed to install a relatively large subsystem, such as a desktop environment, or an office suite, etc.

