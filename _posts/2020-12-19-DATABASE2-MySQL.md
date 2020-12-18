---
title: "생활코딩 DATABASE2 - MySQL"
excerpt: "MySQL"
toc: true
toc_sticky: true
categories:
 - MySQL, Database
tags:
 - MySQL, Database
last_modified_at:
---
이 포스트는 생활코딩 DATABASE2 - MySQL 강의를 듣고 정리한 내용입니다.
MacOS 에서 MySQL을 MariaDB로 대체하여 실행하였습니다.

# 생활코딩 DATABASE2 - MySQL
## MariaDB 설치
### Linux
[Debian 10에서 MariaDB 설치](https://www.digitalocean.com/community/tutorials/how-to-install-mariadb-on-debian-10)
### MacOS
[MacOS에서 MariaDB 설치](https://mariadb.com/kb/ko/installing-mariadb-on-macos-using-homebrew/)

## 서버 실행
MariaDB 서버를 먼저 실행시키기 위해 터미널에서 아래와 같이 입력한다.
> mysql.server start

## 서버 접속
MariaDB 서버에 접속하기 위해 터미널에서 아래와 같이 입력한다.

root 계정에 비밀번호가 없을 경우
> sudo mysql -u root

root 계정에 비밀번호가 있을 경우
> sudo mysql -u root -p

## 스키마의 확인, 생성, 삭제, 사용
```sql
SHOW DATABASES;
SHOW SCHEMAS;
CREATE DATABASE opentutorials;
DROP DATABASE opentutorials;
USE opentutorials;
```
## 테이블 생성
```sql
CREATE TABLE topic(
    id INT(11) NOT NULL AUTO_INCREMENT,
    title VARCHAR(100) NOT NULL,
    description TEXT NULL,
    created DATETIME NOT NULL,
    author VARCHAR(30) NULL,
    profile VARCHAR(100) NULL,
    PRIMARY KEY(id));
```
## INSERT
```sql
DESC topic;
INSERT INTO topic (title, description, created, author, profile) VALUES ('MySQL', 'MySQL is ...', NOW(), 'egoing', 'developer');
SELECT * FROM topic;
```
## SELECT
```sql
SELECT * FROM topic;
SELECT id, title, created, author FROM topic;
SELECT * FROM topic WHERE author = 'egoing';
SELECT * FROM topic WHERE author = 'egoing' ORDER BY id DESC;
SELECT * FROM topic WHERE author = 'egoing' ORDER BY id DESC LIMIT 2;
```
## UPDATE
```sql
UPDATE topic SET description = 'Oracle is ...', title = 'Oracle' WHERE id = 2;
```
## DELETE
```sql
DELETE FROM topic WHERE id = 5;
```
## 테이블 분리
```sql
RENAME TABLE topic TO topic_backup;

CREATE TABLE `author` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(20) NOT NULL,
  `profile` varchar(200) DEFAULT NULL,
  PRIMARY KEY (`id`)
);

CREATE TABLE `topic` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `title` varchar(30) NOT NULL,
  `description` text,
  `created` datetime NOT NULL,
  `author_id` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
);

INSERT INTO `author` VALUES (1, 'egoing', 'developer');
INSERT INTO `author` VALUES (2, 'duru', 'database administrator');
INSERT INTO `author` VALUES (3, 'taeho', 'data scientist, developer');

INSERT INTO `topic` VALUES (1, 'MySQL', 'MySQL is...', '2018-01-01 12:10:11', 1);
INSERT INTO `topic` VALUES (2, 'Oracle', 'Oracle is ...', '2018-01-03 13:01:10', 1);
INSERT INTO `topic` VALUES (3, 'SQL Server','SQL Server is ...', '2018-01-20 11:01:10', 2);
INSERT INTO `topic` VALUES (4, 'PostgreSQL','PostgreSQL is ...', '2018-01-23 01:03:03', 3);
INSERT INTO `topic` VALUES (5, 'MongoDB','MongoDB is ...', '2018-01-30 12:31:03', 1);
```
## JOIN
```sql
SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.id;

SELECT topic.id, title, description, created, name, profile FROM topic LEFT JOIN author ON topic.author_id = author.id;

SELECT topic.id AS topic_id, title, description, created, name, profile FROM topic LEFT JOIN author ON topic.author_id = author.id;
```
## MySQL 클라이언트
- MySQL Monitor
- MySQL Workbench
## 수업을 마치며
- index
- modeling
- backup
  - mysqldump
  - binary log
- cloud
  - AWS RDS
  - Google Cloud SQL for MySQL
  - AZURE Database for MySQL
- Python, PHP, Java mysql api