---
layout: post
title: macOS에 MySQL 설치
tags:
  - macos
  - brew
---

masOS에서 brew를 이용해 mysql를 설치합니다.

(사전 작업)

[Homebrew 설치](2020-12-09-mac-os-brew-install.md)


아래의 명령어로 cask 패키지(Safari, Chrome, Word 등과 같이 그래픽을 통해 작업하는 프로그램을 설치할 수 있게 해주는 패키지)를 설치합니다.
```
brew install cask
```

먼저 Homebrew의 최근 업데이트가 있는지 확인합니다.
```
brew update
```

### MySQL 설치

`brew search mysql`로 현재 brew에서 제공하는 설치할 수 있는 MySQL 버전을 확인합니다.

`brew install mysql` 을 하면 가장 최신 버전을 설치합니다.

```
$ brew install mysql
...
...
We've installed your MySQL database without a root password. To secure it run:
    mysql_secure_installation

MySQL is configured to only allow connections from localhost by default

To connect run:
    mysql -uroot

To have launchd start mysql now and restart at login:
  brew services start mysql
Or, if you don't want/need a background service you can just run:
  mysql.server start
==> Summary
🍺  /usr/local/Cellar/mysql/8.0.22_1: 294 files, 296.5MB
==> Caveats
==> protobuf
Emacs Lisp files have been installed to:
  /usr/local/share/emacs/site-lisp/protobuf
==> mysql
We've installed your MySQL database without a root password. To secure it run:
    mysql_secure_installation

MySQL is configured to only allow connections from localhost by default

To connect run:
    mysql -uroot

To have launchd start mysql now and restart at login:
  brew services start mysql
Or, if you don't want/need a background service you can just run:
  mysql.server start
```

### MySQL 설정
MySQL을 실행시킵니다.

`mysql.server start`

```
$ mysql.server start
Starting MySQL
. SUCCESS!
```

`mysql_secure_installation` 명령어로 MySQL 설정을 진행합니다.

```
Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: n
Please set the password for root here.

New password: (root 비밀번호 입력)

Re-enter new password:
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y (익명 접속 허용 여부, y로 하면 접속시 반드시 -u 옵션 필요)

 ... skipping.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y (root 계정의 원격접속 차단)

 ... skipping.
By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y (테스트 데이터베이스 삭제)
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y (지금까지 진행한 내용 저장)
Success.

All done!
```


`mysql -uroot -p` 비밀번호 입력 후 접속

```
$ mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.22 Homebrew

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> quit (로그아웃 시 exit 또는 quit)
Bye
```

MySQL 서버 종료

```
mysql.server stop
```

MySQL 삭제

```
sudo rm -rf /usr/local/var/mysql
sudo rm -rf /usr/local/bin/mysql*
sudo rm -rf /usr/local/Cellar/mysql
```
