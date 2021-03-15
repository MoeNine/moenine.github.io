---
title: MySQL存在则Update不存在则Insert需求
date: 2021-01-19 11:42:46
tags: MySQL
---

# INSERT ... ON DUPLICATE KEY UPDATE

如果在一个SQL语句中使用了ON DUPLICATE KEY UPDATE，并且要插入的记录中有UNIQUE索引或者主键中造成重复的话，那么就会对旧记录进行UPDATE操作,

如果a字段被声明为UNIQUE并存在值为1的记录，那么下面两个语句效果是类似的。

```mysql
INSERT INTO t1 (a,b,c) VALUES (1,2,3)
  ON DUPLICATE KEY UPDATE c=c+1;

UPDATE t1 SET c=c+1 WHERE a=1;
```







> Ref:https://dev.mysql.com/doc/refman/8.0/en/insert-on-duplicate.html