# MySQL 基础

MySQL 基础知识是面试必考内容。

## 目录
- [[0-MySQL面试核心]] - 核心知识点速览
- [[基本概念]] - MySQL定义、特点
- [[常用命令]] - 数据库操作、表操作、CRUD等

## 高频考点
- MySQL 是什么：开源的关系型数据库，Oracle 旗下，国内使用最广泛
- 内连接 vs 外连接：INNER JOIN 只返回匹配行，LEFT/RIGHT JOIN 返回所有行
- 三大范式：1.列不可分 2.消除部分依赖 3.消除传递依赖
- varchar vs char：varchar 可变长，char 定长（空间换时间）
- DATETIME vs TIMESTAMP：DATETIME 8字节无时区，TIMESTAMP 4字节有时区
- DROP vs DELETE vs TRUNCATE：DROP删表，DELETE删行可回滚，TRUNCATE删全部不可回滚
- UNION vs UNION ALL：UNION去重+排序慢，UNION ALL不去重快
- 记录货币用什么字段类型：Decimal 和 Numeric 类型
- 怎么存储 emoji：utf8mb4 字符集