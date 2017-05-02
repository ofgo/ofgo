# 数据库索引

## 索引

索引是存储引擎用于快速找到记录的一种数据格式，类似书的目录。

索引能提升查询速度，但同时也会增加存储空间，加重插入、删除和修改的负担。

> MySQL中KEY和INDEX是等价的

## 索引操作

* CREATE
	
	```mysql
	CREATE INDEX index_name ON table (col_name);
	ALERT TABLE table ADD INDEX index_name ()
	```

* UPDATE
	
	```mysql
	
	```

* SELECT

	```mysql
	show index from table;
	show keys from table;
	```
	
* DELETE

	```mysql
	DROP INDEX index_name ON table;
	```
	

## 重复索引

重复索引：在相同的列上按照相同的顺序创建相同类型的索引，应避免重复索引。

* 新手常犯的错误是在一个字段上创建了主键、唯一索引和普通索引（KEY），其实，MySQL的唯一限制和主键限制都是通过索引实现的，所以上面实际上创建了三个重复索引；
* 如果创建了联合索引（A, B）那么再创建索引（A）就是冗余索引；


## Reference 

* [MySQL索引详解](http://shanks.leanote.com/post/Mysql%E7%B4%A2%E5%BC%95)



