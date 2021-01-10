# mysql2sqlite

将MySQL的sql文件转换为SQLite3兼容的数据库文件（包括来自`CREATE`块的MySQL`KEY xxxxx`语句）。

## 用法

1. 导出MySQL数据库sql文件

    ~~~~
    mysqldump --skip-extended-insert --compact [选项]... DB_name > dump_mysql.sql  
    # 或
    mysqldump --no-data -u root -pmyPassword [选项]... DB_name > dump_mysql.sql
    ~~~~

1. 将MySql的sql文件转换为SQLite3数据库

    ~~~~
    ./mysql2sqlite dump_mysql.sql | sqlite3 mysqlite3.db
    ~~~~
   
1. **经测试MySql的sql文件中的`DROP TABLE IF EXISTS` `AUTO_INCREMENT` `PRIMARY KEY` `UNIQUE INDEX` 等语句需要删除**

*`mysql2sqlite`和`sqlite3`都可能会向标准输出和标准错误输出写入一些东西，例如：`memory`来自`PRAGMA journal_mode = MEMORY;`是无害的。*

## 开发

该脚本以*awk*编写（已通过gawk测试，但应与原始的awk和闪电般快的mawk一起使用），应完全符合POSIX标准。

它最初是基于最新[分支](https://gist.github.com/bign8/9055981/05e65fd90c469c5eaa730823910c0c5f9de40ab4) 最初的 [mysql2sqlite.sh](https://gist.github.com/esperlu/943776/be469f0a0ab8962350f3c5ebe8459218b915f817) 文件和以下补丁：

* 修复非标准的`COMMENT`字段声明删除
* 忽略`CREATE DATABASE`语句（`USE`语句已被忽略）
* 添加对多条记录的支持`INSERT INTO VALUES`
* 修改对小写SQL语句的支持
* 修复`AUTO_INCREMENT`支持
* 修剪长度超过16个字符的十六进制值，并发出警告
* 如果检测到`IF NOT EXISTS`或`TEMPORARY`，则添加标识符区分大小写警告，（在unix上，sqlite3将临时表名`FILES`视为`files`；换句话说，sqlite3不会发出任何关于`TABLE`和`TEMPORARY TABLE`标识符之间交叉冲突的警告）
* [将`COLLATE xxx_xxxx_xx`语句替换为`COLLATE BINARY`](https://gist.github.com/4levels/0d5da65bf9d70479fbe3/d0ac3d295dc5e2f72411ad06c07a22931368a1b7)
* [处理`CONSTRAINT FOREIGN KEY`](https://gist.github.com/BastienDurel/7f413d13d7b858aef31c/922be110d011b9da340ae545372214b597ad7b84)

请随意**贡献**（最好是通过发出pull请求）！

## 证书

MIT

## 历史

* @esperlu于2011年在GitHub上创建了最初的gist版本
* 因为@esperlu停止了对gist的研究，也没有做出回应，所以许多不同的贡献者创建了分支，并做出了不同的修改
* @dumblob于2015年8月接手，合并了所有分支中最重要的补丁，以及在Drupal DB上测试的许多自己的补丁
* @dumblob在假定最初仓库已发布到公共领域下添加了MIT证书，因为尽管进行了重大更改，但这并不是一个纯净的工程
* 2016-05-11 17:32 UTC+2 [@esperlu宣布](https://github.com/dumblob/mysql2sqlite/issues/2 ) MIT是一个合适的证书（也可追溯）[original gist](https://gist.github.com/esperlu/943776 )已弃用。
