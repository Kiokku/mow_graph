## Keys
	- ### Primary Key
		- generally an integer auto-increment field - 主键
			- Never use your logical key as the primary key. - 不要使用逻辑键作为主键
			- Relationships that are based on matching string fields are less efficient than **integers**. - 使用整数比使用字符串(GUIDs)更高效
	- ### Logical Key
		- what the outside world uses for lookup - 逻辑键，具有实际意义的键，用作搜索等
	- ### Foreign Key
		- generally an integer key pointing to a row in another table - 外键
			- A foreign key is when a table has a column containing a key that points to the primary key of another table. - 外键是其他表的主键
			- **Integer**
- ## Create Relational Database
	- ```
	  CREATE TABLE artist (
	  
	  id SERIAL,
	  
	  name VARCHAR(128) UNIQUE,
	  
	  PRIMARY_KEY(id)
	  
	  );
	  
	  CREATE TABLE album (
	  
	  id SERIAL,
	  
	  title VARCHAR(128) UNIQUE,
	  
	  artist_id INTEGER REFERENCES artist(id) ON DELETE CASCADE,
	  
	  PRIMARY KEY(id)
	  
	  );
	  ```
	- `SERIAL`、`PRIMARY_KEY` - Primary Key
	- `UNIQUE` - Logical Key
	- `REFERENCES` - Foreign Key
	- `ON DELETE CASCADE` - 级联删除，Foreign Key
- ## JOIN
	- The JOIN operation links **across several tables** as part of a **SELECT** operation.
	- ```
	  SELECT album.title, artist.name
	  
	  FROM album JOIN artist
	  
	  ON album.artist_id = artist.id;
	  ```
	- `JOIN` table2 `ON`table1.ForeignKey = table2.PrimaryKey
	- `INNER JOIN`: 连接时，不匹配的组合会被筛除
	- `CROSS JOIN`:  连接所有组合，没有`ON` 语句
	-
	-