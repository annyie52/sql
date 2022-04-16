# AWS

## EC2

- 클라우드에서 확장 가능한 컴퓨터
- 회사가 개인이 서버를 구성할 때 비용과 시간이 많이 드는데, 아마존에서 미리 제공하는 서버 컴퓨터를 이용해 서비스를 위한 각종 세팅을 저렴한 비용으로 빠르게 구성 가능하다.
- `ssh`를 이용해 어디에서든 접속할 수 있게 한다.



### EC2 인스턴스 만드는 순서

1. 인스턴스 시작 버튼 누르기
2. Ubuntu 검색
3. **`Ubuntu Server 20.04 LTS (HVM)`** `t2.micro` 인스턴스
4. 태그 추가 (이름을 지정한다고 생각하기)
5. 보안 그룹 설정 (방화벽, 허용된 사람만 접속할 수 있게 하기)
   - SQL을 사용할 예정이면 유형을 MYSQL로 설정
   - 전체 오픈(0.0.0.0/0, ::/0)
6. **키 페어 설정**(키 페어가 있어야지 접속할 수 있으므로 지우면 안 된다)



### Amazon EC2 인스턴스 세부 정보 확인

1. 퍼블릭 IPv4 주소
   - 외부에서 접속할 IP 주소
   - 이 IP 주소를 이용하면 공간 제약 없이  EC2에 접속이 가능
   - 인스턴스 정지 후 다시 시작(재 부팅) 하면 퍼블릭 IPv4 주소가 바뀐다.
2. 프라이빗 IPv4 주소
   - EC2 내의 서비스들 끼리 통신 할 때 필요한 주소 입니다.
   - 즉 하나의 컴퓨터 내부에서 통신을 하기 위해 필요한 IP 주소입니다.



## EC2에 MySQL 설치 및 설정하기

```
ubuntu@ip-<YOUR PRIVATE IP>:~$ chmod 400 my-sql22.pem
ubuntu@ip-<YOUR PRIVATE IP>:~$ ssh -i "my-sql22.pem" ubuntu@ec2-3-37-129-46.ap-northeast-2.compute.amazonaws.com

# 최신 버전 프로그램을 다운하기 위해 apt-get 업데이트
ubuntu@ip-<YOUR PRIVATE IP>:~$ sudo apt update

# mysql 다운로드 및 설치
ubuntu@ip-<YOUR PRIVATE IP>:~$ sudo apt install mysql-server-5.7

# 보안 설정
ubuntu@ip-<YOUR PRIVATE IP>:~$ sudo mysql_secure_installation
```

1. 패스워드 설정 /  **n**을 입력

   - ```
     Would you like to setup VALIDATE PASSWORD plugin? Press y|Y for Yes, any other key for No: n
     패스워드 설정 : 1111
     ```

2. 기본적으로 설정 되어있는 익명 사용자를 삭제 /  **y**를 입력

   - ```
     Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
     ```

3. 내부에서만 로그인 할 수 있도록 설정 / **n**을 입력

   - ```
     Disallow root login remotely? (Press y|Y for Yes, any other key for No) : n
     ```

4. 테스트 데이터베이스를 제거 /  **y**를 입력

   - ```
     Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
     ```

5. 변경된 설정을 다시 불러오기 / **y**를 입력

   - ```
     Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
     ```

```
ubuntu@ip-<YOUR PRIVATE IP>:~$ sudo mysql -u root -p
mysql> SELECT user,authentication_string,plugin,host FROM mysql.user;
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '비밀번호';

# 설정 즉시 적용
mysql> FLUSH PRIVILEGES;
mysql> exit


ubuntu@ip-<YOUR PRIVATE IP>:~$ sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
  #bind-address의 b에 커서를 두고 i를 누르면 insert 버튼이 뜸 bind-address 앞에 #을 붙여 줌
  # 그 아래줄에 bind-address = 0.0.0.0을 입력하고 esc 후 :wq 입력 후 엔터

ubuntu@ip-<YOUR PRIVATE IP>:~$ mysql -u root -p
mysql> CREATE USER '본인의 계정명'@'%' IDENTIFIED BY '비밀번호';
mysql> GRANT ALL PRIVILEGES ON *.* to '본인의 계정명'@'%';
mysql> FLUSH PRIVILEGES;
mysql> exit

ubuntu@ip-<YOUR PRIVATE IP>:~$ sudo systemctl restart mysql.service
```

