---
layout: post
title: "[MySQL] 파일 데이터 import/export"
subtitle:
categories: MySQL, Database
tags: [Da, MySQL]
---

# MySQL data import/export

- 프로젝트에서 공공데이터와 같은 외부 데이터를 사용하게 되면 보통 .xls나 .csv 형태의 데이터를 사용한다.
- 이런 데이터들은 대용량인 경우가 많은다. 이런 **대용량 파일 데이터**를 쉽게 데이터베이스 테이블에 저장/내보내기 위해서 **import/export** 하는 법을 알아보자 (with.MySQL)

## import

파일 데이터를 테이블에 저장하기

### 1. 테이블 생성

```SQL
CREATE TABLE `medi` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `sido` VARCHAR(45) NULL,
  `sigugun` VARCHAR(45) NULL,
  `medi_nm` VARCHAR(45) NULL,
  `addr` VARCHAR(100) NULL,
  `weekday_time` VARCHAR(100) NULL,
  `sat_time` VARCHAR(100) NULL,
  `holiday_time` VARCHAR(100) NULL,
  `tel` VARCHAR(45) NULL
  PRIMARY KEY (`id`));
```

> id 는 자동으로 생성되므로 따로 만들지 않음

### 2. mysql 에서 파일데이터를 table에 저장하기

- `TERMINATED BY [구분자]` : 칼럼이 어떤 구분자로 구분되어 있는지
- `ignore n lines` : 첫 n줄 무시 ( 보통 첫불에 칼렁명이 있는 경우 사용한다.)
- 예시 쿼리

  ```SQL
  # , 로 분리된 csv 파일인 경우
  LOAD DATA INFILE '파일경로/파일명.csv' INTO TABLE [table name] TERMINATED BY ',';

  # tab 으로 분리된 txt 파일인 경우
  LOAD DATA INFILE '파일경로/파일명.txt' INTO TABLE [테이블명] FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' ignore 1 lines;

  # 만약 칼럼을 지정한다면 뒤에 ()안에 지정할 칼럼명을 적으면 된다.
  load data infile '파일경로/파일명.txt'
  into table [데이블명]
  FIELDS TERMINATED by '\t' ignore 1 lines
  (칼럼명1, 칼럼명2, ,,, );
  ```

- 위 테이블에 맞는 데이터를 준비하고 아래와 같이 쿼리를 작성하였다.
  ```SQL
  load data infile 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/data.txt'
  into table medi
  FIELDS TERMINATED by '\t' ignore 1 lines
  (sido, sigugun, medi_nm, addr, weekday_time, sat_time, holiday_time, tel);
  ```

### 3. 완료 화면

```
625 row(s) affected Records: 625  Deleted: 0  Skipped: 0  Warnings: 0	0.000 sec
```

## export

`into outfile` 이용

```sql
select * from medi
into outfile 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/export_data.csv'
FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n';
```

## import 시 주의 사항 및 에러

### 주의 사항

- 파일 경로 입력시, / 로 경로 구분
- id 값 부여 해줘야 함.

### 에러1

> error code 1290. the mysql server is running with the --secure-file-priv option
> so it cannot excute this statement

- 설정된 파일 입출력 경로로 인한 에러
- 해결

0. 파일 입출력 경로 확인

   ```shell
   select @@GLOBAL.secure_file_priv;
   ```

   ![Untitled](/assets/images/posts/db/2022-06-14-1.png)

1. 해결 방법 1

- 해당 경로에 파일을 넣는다.

2. 해결 방법 2

- 파일입출력 경로를 변경한다
  1. 관리자 권한으로 cmd 실행
  2. MySQL 의 **my.ini** 파일이 있는 폴더로 이동(ProgramData > MySQL > MySQL Server 8.0)
  ```shell
  C:\WINDOWS\system32>cd C:\ProgramData\MySQL\MySQL Server 8.0
  ```
  ![Untitled](/assets/images/posts/db/2022-06-14-2.png) 3. my.ini 파일 열기
  ```shell
  C:\ProgramData\MySQL\MySQL Server 8.0>NOTEPAD my.ini
  ```
  4. secure-file-priv 경로 변경
  ```
  C:\ProgramData\MySQL\MySQL Server 8.0>NOTEPAD my.ini
  ```
  5. MySQL 서비스 재시작
  ```
  //종료
  > net stop MySQL80
  //시작
  > net start MySQL80
  ```
  ![Untitled](/assets/images/posts/db/2022-06-14-3.png)

### 에러2

> ERROR 1366 (HY000): Incorrect integer value: '\FEFF1 ' for column 'id' at row 1

- 정수형 칼럼에 문자형 이나 null 이 입력될 때 발생
- 해결 => 파일 인코딩 변경( utf-8(DOM) → utf8 )
  - 메모장에 파일 로드 후 인코딩 확인
  - **인코딩 utf8로 저장**
