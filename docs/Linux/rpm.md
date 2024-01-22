---
layout: default
title: RPM
parent: Linux
permalink: /docs/Linux/rpm
tags:
- linux
---

## RPM
---
> The Red Hat Package Manager (RPM) is used by a number of major distributions (and their close relatives) to control the installation, verification, upgrade, and removal of software on Linux systems. The low-level rpm program can perform all these operations, either on just one package, or on a list of packages. Operations which would cause problems (such as removing a package that another package depends on, or installing a package when the system needs other software to be installed first) are blocked from completion.

### Package File Names
---
- The standard naming format for a binary RPM package is:
	`<name>-<version>-<release>.<distro>.<architecture>.rpm`
	예: sed-4.5-2.el18.x86_64.rpm

- The standard naming format for a source RPM package is:
	`<name>-<version>-<release>.<distro>.src.rpm`
	예: sed-4.5-2.el18.src.rpm

- `<distro>` 부분은 패키지 출처 리포지토리를 주로 명시하는데 쓰임

### RPM Database and Helper Programs
---
- `/var/lib/rpm` 은 RPM 데이터베이스 파일을 보관하는 시스템 디렉토리
- 업데이트는 반드시 rpm 프로그램을 이용해서 수행
- 데이터베이스 파일 corruption 등으로 리빌드가 필요할 경우
	1. /var/lib/rpm 내 컨텐츠 백업 `$ sudo cp -a /var/lib/* rpm_BACKUP`
	2. rpm 데이터베이스 리빌드 `$ sudo rpm --rebuilddb`
	3. 신규 데이터베이스 디렉토리와 기존 디렉토리의 컨텐츠 차이점 비교 `$ ls -l rpm rpm_BACKUP`
	4. 시스템 내의 모든 rpm 목록 조회. 이 쿼리가 정상 작동하면 신규 데이터베이스 파일이 정상이란 뜻 `$ rpm -qa | tee /tmp/rpm-qa.output`
		- `tee` 명령은 입력을 받아 파일에 쓰는 동시에 화면에 출력하는 기능을 제공
	

### Queries
---
- 모든 rpm 쿼리는 -q 옵션을 포함한다

    -f: allows you to determine which package a file came **from**
    -l: **lists** the contents of a specific package
    -a: **all** the packages installed on the system
    -i: **information** about the package
    -p: run the query against a package file instead of the package database

- 인스톨 된 패키지(예: bash) 버전의 확인?
	- `$ rpm -q bash`

- 패키지 파일 출처 확인
	- `$ rpm -qf /bin/bash`

- 이 패키지로 인해 설치되는 파일들의 확인?
	- `$ rpm -ql bash`

- 설치된 패키지 내의 파일 확인
	- `$ rpm -qilp package.rpm`

- 패키지 정보 확인(패키지 데이터베이스로부터)
	- `$ rpm -qi bash`

- 패키지 정보 확인(패키지 파일로부터)
	- `$ rpm -qip foo-1.0.0-1.noarch.rpm`

- 시스템에 설치된 모든 패키지 확인
	- `$ rpm -qa`

- 여러 커맨드를 combine 해서 사용할 수도 있음
	- 예: /etc/logrotate.conf 파일이 포함된 rpm 패키지의 정보 및 포함된 모든 파일 정보 출력
		- `$> rpm -qil $(rpm -qf /etc/logrotate.conf)`

### Queries: Useful Parameters
---
	- `--whatprovides`, `--whatrequires`

- `--whatrequires`: 특정 rpm 패키지가 어떤 다른 패키지에 의존하고 있는지 찾음
- `--whatprovides`: 어떤 rpm 패키지가 특정한 파일이나 기능을 제공하는지 확인
	- 예를 들어, 특정 라이브러리나 실행 파일이 어떤 패키지에 포함되어 있는지 알고 싶을 때 이 옵션을 사용

- 예)
	- `$ rpm -qp --whatrequires bash`
		- output 예시: `foo-1.0.0-1.noarch.rpm`, `rpm-build-4.11.3-21.el7.x86_64`
	- `$ rpm -q --whatprovides bash
		- output 예시: `bash-1.0.6-13.el7.x86_64.rpm`

### Verifying Packages
---
    
    S: filesize differs
    M: mode differs (permissions and file type)
    5: MD5 sum differs
    D: device major/minor number mismatch
    L: readLink path mismatch
    U: user ownership differs
    G: group ownership differs
    T: mTime differs 

- 패키지 파일 사이즈, 체크섬, 최종 수정 시간 정보 확인
	- `$> rpm -V bash`

- 만일 패키지 내의 특정 파일을 다른 위치로 이동시키거나 제거할 경우 아래와 같이 `missing`이 출력됨
```
$ sudo mv /sbin/logrotate /sbin/logrotate_KEEP
$ rpm -V logrotate
```

Output:
```
S.5....T. c /etc/logrotate.conf
missing    /usr/sbin/logrotate
```


### Installing Packages
---
- rpm 패키지 인스톨 시 자주 사용되는 명령 옵션은 아래와 같다.
	- `$> rpm -ivh 패키지명`
		- -i: 인스톨
		- -v: 디버깅 모드
		- -h: 설치 진행 정보를 hash 마크로 표시


### Uninstalling Packages
---
- -e 옵션으로 (erase) 하지만 해당 패키지가 다른 패키지에서 의존성이 있을 경우 제거 되지 않음
- --test 옵션을 주어 제거 전에 의존성을 체크할 수 있음
	- 예: `$> rpm --test -e <package name>`
		- 의존성이 있는 다른 패키지가 확인될 경우 error: Failed dependencies: 출력됨

### Updating Packages
---
> 특정 패키지가 인스톨 되어있다면 업데이트 하는 패키지가 기존 패키지를 대체한다.
> 인스톨 되어 있지 않다면 새로 인스톨 한다

`$> rpm -Uvh bash-4.4.19-12.el8.x86_64.rpm`

- -U: 업데이트 또는 인스톨
	- 이미 있는 패키지에 대해 -i 옵션으로 인스톨을 시도할 경우 overwrite 에러 출력

- 만일 -U 옵션을 통해 이전 버전으로 다운그레이드를 원한다면 `--oldpackage` 옵션을 주어 기존 파일을 대체한다 
	- `$> rpm -Uvh --oldpackage <패키지 버전>`

### Freshening Packages
---
> - 이전 버전이 인스톨 되어 있다면 새로운 버전으로 업그레이드 한다
> - 기존 버전과 새로고침한 패키지 버전이 같을 경우 아무 작업도 하지 않는다
> - 인스톨된 패키지에 버전이 없다면 무시된다

`$> sudo rpm -Fvh *.rpm`

- -F 옵션은 여러 개의 새로운 패치를 다운로드하고, 이미 설치 되어있는 패키지들에 한해서만 업그레이드를 하려고 할 때 유용하다. 새로운 패키지를 설치하지 않는다.


### Upgrading the Linux Kernel
---
> 새 커널을 설치할 경우 재부팅이 필요하다
> -U 옵션으로 커널을 설치하지 말아야한다: 현재 실행되고 있는 커널이 대체되기 때문에 복구가 불가하다.
> -i 옵션으로 커널을 설치해야 재부팅 이후 문제가 생겼을 때 기존 커널/새 커널 버전이 모두 유지되기 때문에 복구 가능

`$> rpm -ivh kernel-{version}.{arch}.rpm`

- -i 옵션으로 설치한 커널은 GRUB configuration 파일에 자동으로 추가되고 default boot 옵션으로 선택된다.

- 시스템 용량이 부족한 것이 아니라면 만일의 상황을 대비하여 항상 2개 버전의 실행 가능한 커널을 보유할 것
- 이전 커널 버전을 반드시 제거해야 할 경우 아래의 명령을 사용할 수 있다

`$> sudo dnf remove --oldinstallonly`


### Archive .rpm files into .tar, .tar.gz, tar.bz2
---
> `rpm2archive` 명령은 RPM 패키지를 아카이브 파일로 변환하는데 사용하는 도구로 일반적인 아카이브 형식인 tar,tar.gz,tar.bz2으로 변환하는데 사용 가능하다. 주로 소스 코드나 실행 파일을 포함하는 rpm 패키지를 다른 시스템에 전송하거나 배포하기 위해 사용된다.

`$> rpm2archive bash-xxxx.rpm`

아래와 같이 파이핑하여 .rpm.tgz 파일로 아카이빙 할 수도 있음

`$> cat bash-xxxx.rpm | rpm2archive - | tar -xvz`


### Extract .rpm files 
---

`$> rpm2cpio bash-xxxx.rpm | cpio --extract --make-directories`

- 현재 사용자가 위치한 디렉토리 경로 내에서 extract 됨

