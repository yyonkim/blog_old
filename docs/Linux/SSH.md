---
layout: default
title: SSH
parent: Networking
grand_parent: Linux
permalink: /docs/Linux/Networking/SSH/
nav-order: 2
---

# SSH
---
> 네트워크 상의 다른 Remote 시스템에 로그인하거나, Remote 시스템에서 명령 실행 및 다른 시스템으로의 파일 복사 시 암호화 기법을 사용하여 안전한 통신을 가능하게 해 주는 프로토콜. Secure Shell 또는 Secure Socket Shell이라 부르며 기본적으로 22번 포트를 사용한다.

## SSH Main Functions
---
- 보안 접속을 통한 rsh, rcp, rlogin, rexec, telnet, ftp 등을 제공.
- IP spoofing (IP스푸핑, 아이피 위/변조 기법중 하나)을 방지하기 위한 기능을 제공.
- X11 패킷 포워딩 및 일반적인 TCP/IP 패킷 포워딩을 제공.
- 공개키 암호화 기법(Private-Public 한 쌍)을 사용하며, 공개 키가 Remote 서버에 위치하고 개인 키가 로컬 말단에 위치할 경우 암호 입력은 불필요
	- Public Key: Public, can be stored on the server or anywhere else.
	- Private Key: Super Secret!, should be securely stored only by you.

References:
[+] https://en.wikipedia.org/wiki/Secure_Shell

## SSH Configuration Files
---
```bash
.ssh directory:

    id_rsa: the user's private key
    id_rsa.pub: the user's public key
    authorized_keys: public keys that are permitted to log in
    known_hosts: hosts from which logins have been allowed in the past
    config: file for specifying various options
```

- authorized_keys: 일반적으로 서버 측 유저 홈 디렉토리에 위치하며 접속 시 사용되는 개인키와 대조시 사용

## Best practices on ssh configuration parameters (OpenSSH)
---

References:
[+] https://infosec.mozilla.org/guidelines/openssh

## SSH Configuration File Precedence
---
> 아래 순서 중 첫 번째로 매치되는 컨텐츠가 접속에 사용됨

1. Command line options
2. ~/.ssh/config
3. /etc/ssh/ssh_config

> 예를 들어 아래와 같은 경우, `$> ssh server1` 로 접속시 아래의 구성 정보가 적용됨
```bash
/home/yon/.ssh/config

Host server1
         HostName 192.168.0.123
         User yon
         Port 22
         IdentityFile /home/yon/.ssh/custom 
```


