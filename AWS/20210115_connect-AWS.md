---
title: ［서버 / Server］ AWS를 이용한 서버 구축 (2) 외부 접속
tags: ['Server', 'AWS', 'Nginx', 'PHP', 'MySQL']
categories: ['Server']
date: 2020-10-03
---

## Situation
현재 `AWS` 클라우드 서비스에 서버를 구축해놨다.
| Server Program | Back-end Language | DBMS (+DB) |
| :--: | :--: | :--: |
| `Nginx` | `PHP` | `MySQL` |

`chrome` 웹 브라우저를 클라이언트라고 생각하고 서버에 외부 접속하는 방법을 알아보겠다.

### Nginx
![inbound-nginx](https://user-images.githubusercontent.com/46131688/104551834-f4eb6b00-567a-11eb-9008-8d7767a0e092.png)

인바운드 규칙에 `HTTP`를 추가하고 인스턴스의 IP 주소로 들어가면 **Welcome to nginx!** 문장이 우릴 맞아준다. `nginx`가 잘 설치된 것은 물론 외부 접속에 성공했다는 것을 의미한다.
![nginx](https://user-images.githubusercontent.com/46131688/104551840-f7e65b80-567a-11eb-8df4-3df2aa6b71e4.png)

### PHP
#### phpinfo 파일 작성
```sh
$ sudo vi /var/www/html/phpinfo.php
<?php
phpinfo();
?>
```
![putty-php](https://user-images.githubusercontent.com/46131688/104551855-fc127900-567a-11eb-81a0-34306b63a860.png)

#### phpmyadmin 설치
`phpMyAdmin`은 웹 브라우저에서 *MySQL* 이나 *MariaDB* 를 관리할 수 있게 해주는 소프트웨어이다.
```sh
$ sudo apt install phpmyadmin
# symbolic link
$ sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
```
이후 설치 과정은 [갈루아의 반서재](https://antilibrary.org/1901)라는 블로그에 굉장히 자세하게 나와 있으니 참고하면 될 것 같다.

### MySQL
```sh
$ sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
...
bind-adress         = 0.0.0.0
...
```
![inbound-mysql](https://user-images.githubusercontent.com/46131688/104551865-00d72d00-567b-11eb-90ee-01ae185c0b0f.png)

인바운드 규칙에 `MySQL`을 추가하면 **3306** 포트가 자동으로 설정된다. *AWS* 에 접속할 계정을 하나 생성해보자.
```sh
$ mysql -u root -p

# 새로운 계정 생성 및 권한 부여
# GRANT 권한종류 ON 대상 TO 계정명 IDENTIFIED BY 암호 [WITH GRANT OPTION];
mysql> grant all privileges on *.* to 'sangmin'@'%' identified by '[PASSWORD]' with grant option;
# 변경 내용 적용
mysql> flush privileges;
mysql> exit;
```

```sh
# 방화벽에서 3306 포트 허용
$ sudo ufw allow out 3306/tcp
$ sudo ufw allot in 3306/tcp

$ sudo service mysql restart
```

#### MySQL Workbench에서 확인
새로운 커넥션을 만들어 *Hostname* 에는 `AWS instance IP`를, *Username* 에는 위에서 생성한 `USER_NAME`을 입력하여 테스트 커넥션을 진행한다. 아래와 같으면 정상적으로 연결된 것이다.
![mysql-connect](https://user-images.githubusercontent.com/46131688/104551874-046ab400-567b-11eb-91a0-6608c9cf0fb8.png)
