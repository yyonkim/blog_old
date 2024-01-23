---
layout: default
title: User Account Management
parent: Linux
nav-order: 1
tags:
- linux
---
# User Account Management

## /etc/passwd
---
```bash
$ cat /etc/passwd | grep ec2-user
ec2-user:x:1000:1000:EC2 Default User:/home/ec2-user:/bin/bash
```
```bash
- User name: ec2-user
- User password: x
- User identification number (UID): 1000
- Group identification number (GID): 1000
- Comment or GECOS information: EC2 Default User
- Home directory : /home/ec2-user
- Login shell: /bin/bash
```
## Block User login
---
> 유저 로그인 셸을 /sbin/nologin 으로 변경
> /etc/nologin.txt 파일에 원하는 메시지를 넣어 놓으면 해당 유저의 로그인을 막을 수 있음

```bash
yonkim:x:1002:1002::/home/yonkim:/sbin/nologin

# sudo su yonkim
This account is currently not available

$ sudo su yonkim
This account is currently not available
```

## User IDs and /etc/passwd
---
> User UID는 /etc/login.defs에 정의된 UID_MIN 값 부터 오름차순으로 부여
```bash
cat /etc/login.defs | grep UID_MIN
UID_MIN = 1000
```

> 위와 같이 설정되어 있는 경우, 시스템에 새로운 유저가 생성될 때마다 UID는 1000 이상만 부여됨

```bash
ec2-user:x:1000:1000:EC2 Default User:/home/ec2-user:/bin/bash
ssm-user:x:1001:1001::/home/ssm-user:/bin/bash
```

> 패스워드 만료 기한 설정
```bash
$ sudo chage -E 2001-09-11 유저명
```

> 특정 유저의 패스워드 만료 관련 정보 조회
```bash
$ chage -l ec2-user
Last password change								: Dec 03, 2023
Password expires									: never
Password inactive									: never
Account expires										: Dec 31, 2025
Minimum number of days between password change		: 0
Maximum number of days between password change		: 99999
Number of days of warning before password expires	: 7
```

## Startup Files Order
---
> When you login to Linux, /etc/profile is always read and evaluated. Next, the following files are searched for in this order:
	- ~/.bash_profile: Login shells configurations
	- ~/.bash_login: Login initialization
	- ~/.profile: overrides /etc/profile

---

## Creating User Account with `useradd`
---
> useradd allows for default operation:
```bash
sudo useradd yonkim
```

1. /etc/login.defs에 정의된 UID_MIN보다 높은 값의 UID가 yonkim에게 부여됨
2. 동일 GID=UID의 유저그룹이 yonkim의 primary 유저 그룹으로 배정
3. /home/yonkim 생성 (owner: yonkim)
4. yonkim의 기본 로그인 셸은 /bin/bash
- /etc/default/useradd에 정의된 로그인 셸에 따라 부여되며 OS별로 상이할 수 있음	
5. /etc/skel 의 컨텐츠(startup files)가 /home/yonkim에 복사됨
6. /etc/passwd 파일에 yonkim의 엔트리에 !!가 추가됨, 향후 admin에 의한 패스워드 부여 필요

> tweak useradd command options when executed
```
$ sudo useradd -s /bin/csh -m -k /etc/skel -c "Yon default user" yonkim
```
## Sudo, Su, Su -
---

- sudo: Root가 아닌 유저가 Root 권한을 빌려 명령을 실행 (명령 주체: 현재 유저)
- su: Root 셸 사용 | # /root/.bashrc  사용
- su -: Root 셸 사용 | $user의 ~/.bashrc 사용

Note: sudoers에서 허가된 유저의 경우 Root 패스워드 불필요 
