## Auto Increment
	- ```
	  CREATE TABLE users (
	    id SERIAL, 
	    name VARCHAR(128), 
	    email VARCHAR(128) UNIQUE,
	    PRIMARY KEY(id)
	  );
	  ```
	- `SERIAL`: 自增序号
	- `PRIMARY KEY`: 索引主键
- ## Indexes
	- **Indexes** are shortcuts
	- ### B-Trees
		- 有序的
		- 平衡树
	- ### Hash
		- maps large data sets to smaller data sets
		- 精确搜索
		- 主键、GUID
-