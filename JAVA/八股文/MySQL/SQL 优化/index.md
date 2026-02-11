# SQL 优化

MySQL SQL 性能优化是面试高频考点。

## 目录
- [[0-MySQL面试核心]] - 核心知识点速览
- [[慢查询分析]] - explain 分析、执行计划
- [[索引优化]] - 索引使用与优化技巧

## 高频考点
- 慢查询优化：explain 分析 + 索引优化 + 避免全表扫描
- SQL 查询语句的执行顺序：FROM → ON → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
- in 和 exists 的区别：in 用 hash 连接，exists 用 loop 循环，根据表大小选择
- count(1)、count(*) 与 count(列名) 的区别：执行效果和速度差异
- drop、delete 与 truncate 的区别：类型、回滚、删除内容、速度
- UNION 与 UNION ALL 的区别：UNION去重+排序慢，UNION ALL不去重快