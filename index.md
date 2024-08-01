# Indexes
.indexes --list all indexes
pragma index_list('table name') -- indexes on tables
pragma index_info('index name');

```sql 
select ((count(distinct birthday) * 1.0) / count(*) ) as selectivity from users;
```
## Composite
- left to right no skipping
- stops as first range operation

## Partial index
create index aaa on table(some_column) where other_column=2

## Can also create indexes on expression directly

## Automatic indexes
Created sometimes by sqlite before running expensive query, they are created only for running query

## Duplicated indexes
After some time indexes data are accumulating so there is a possibility to remove some off them because they are already covered. There could be an issue:
``` 
sqlite> create index email_pro on users(email, is_pro);
sqlite> .eqp on
sqlite> select * from users Where email='xxx.aaa' and is_pro=1;
QUERY PLAN
`--SEARCH users USING INDEX email_pro (email=? AND is_pro=?)
sqlite> select * from users Where email='xxx.aaa';
QUERY PLAN
`--SEARCH users USING INDEX email_pro (email=?)
sqlite> select * from users Where email='xxx.aaa' order by id;
QUERY PLAN
|--SEARCH users USING INDEX email_pro (email=?)
`--USE TEMP B-TREE FOR ORDER BY
sqlite> create index email on users(email);
sqlite> select * from users Where email='xxx.aaa' order by id;
QUERY PLAN
`--SEARCH users USING INDEX email (email=?)
```
here id was = rowid so index on email was in fact email,id and index email_pro was email,is_pro,id. When we wanted to sort the last query it could not match fully the index email_pro.
It would work if outside email condition it would be condition for is_pro
``` 
sqlite> drop index email;
sqlite> select * from users Where email='xxx.aaa' and is_pro=1 order by id;
QUERY PLAN
`--SEARCH users USING INDEX email_pro (email=? AND is_pro=?)

```
## Foreign key

```sql

sqlite> pragma foreign_keys = true;
sqlite> CREATE TABLE child_table (
(x1...>     child_id INTEGER PRIMARY KEY,
(x1...>     parent_id INTEGER,
(x1...>     info TEXT,
(x1...>     FOREIGN KEY (parent_id) REFERENCES parent_table(parent_id)
(x1...> );
sqlite>
sqlite> CREATE TABLE parent_table (
(x1...>     parent_id INTEGER PRIMARY KEY,
(x1...>     data TEXT
(x1...> );
sqlite>
sqlite> .eqp on
sqlite> select * from parent_table
   ...> ;
QUERY PLAN
`--SCAN parent_table
sqlite> insert into parent_table values(1,'aaa');
sqlite> insert into parent_table values(2,'aaa');
sqlite> insert into child_table values(1,1,'aaa');
sqlite> insert into child_table values(2,20,'aaa');
Runtime error: FOREIGN KEY constraint failed (19)
sqlite> insert into parent_table values(3,'aaa');
sqlite> insert into parent_table values(4,'aaa'),(5,'bbbb'),(6,'aaaa');
QUERY PLAN
|--SCAN 3-ROW VALUES CLAUSE
`--SCAN child_table

```
