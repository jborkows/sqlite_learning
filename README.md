.headers on <- adds headers
.mode changes way of display
.mode json
.mode markdown
.separator "," <- changes separator in list mode to ,
.mode qbox <- display like markdown
.mode insert <- generates insert
.shell clear <- executes shell script
.width column_width other_column_width etc... (without anything resets)
.dbconfig
.schema prints out current schema with indexes

## Execute things to file
.mode insert
.output some_file.sql
select * from users; <- will write result in form of inserts into some_file.sql

.mode insert
.once some_file.sql <- will print to file only next command

## Other .commands
- .schema 
- .expert suggestions for how to improve next search
- .tables
## pragma
- pragma pragma_list <-- all pragmas
- pragma compile_options;

## Read csv into virtual column
.load csv <- extension has to have installed libsqlite3-dev
create virtual table temp.t1 using csv(filename="aa.csv", header=true)


```sql
select * from generate_series(0,100)
```

## Types

[types](types.md)
## Journal modes
- rollback
creates for transaction second file with copy of original data. Modification happen:
 - commit -> separate file can be deleted
 - rollback -> page is replaced with copied one
- wal write ahead log
creates separate file for writing and append, every some time "checkpoints" happen and state is merged. Pragma for journal is persistent.
busy_timeout pragma decides how much writer waits for possibility to write. By default is 0 <- don't wait -> it is pragma per connection.
## Transaction modes
immediate, exclusive, deferred -> in wall immediate and exclusive are the same, deferred is default
Warning if started with deferred and trying to write busy timeout will not wait!!! It will wait only if transaction was started as immediate.

## Vacuum
Positives:
- could free space
- reduces fragmentation
Negatives:
- could be slow
- requires additional space (it copies whole db)
- requires exclusive lock

##  Statistics
```sql
sqlite> select * from sqlite_stat1;
Parse error: no such table: sqlite_stat1
sqlite> pragma optimize(0x03); -- dry run
ANALYZE "main"."users"
ANALYZE "main"."bookmarks"
ANALYZE "main"."categories" -- can be run as standalone
sqlite> pragma optimize; -- can be done partialy using pragma analysis_limit;
sqlite> select * from sqlite_stat1;
users|bday|989908 87
users|name|989908 334 2
users|email|989908 1
bookmarks|user_id|5018199 6
categories|pid|15 3

```
## Pragma
pragma synchronous=NORMAL -- how the data are written to disk, 0 <- trust that data will be saved
pragma synchronous=OFF -- could be useful during import of large of amount of data
pragma cache_size=2000; -- -2000 means 2000kB, +2000 means 2000 pages
pragma temp_store = memory; -- create temporal table in memory
pragma foreign_key = true; -- enable foreign keys

## Explain plan
```sql
explain select * from aaa;
explain query plan select * from aaa;
.eqp on -- explain query plan
.eqp off
```

## Indexes
[index](index.md)

## Subqueries
Correlated sub query (one that references outside query) will be executed for each row from outer select part.
## CTE
with clause...
Recursive cte
```sql 
sqlite> with recursive numbers as (select 1 as n union all select n+1 from numbers where n < 10) select * from numbers;
QUERY PLAN
|--CO-ROUTINE numbers
|  |--SETUP
|  |  `--SCAN CONSTANT ROW
|  `--RECURSIVE STEP
|     `--SCAN numbers
`--SCAN numbers
1
2
3
4
5
6
7
8
9
10

```
important use union all to eliminate need for double checking. Recursive keyword is not required. Please add recursive condition stop, even limit 10.
## Window functions
[Window functions](window_functions.md)
## Nulls fun
[Null fun](nulls_fun.md)
## Upserts
insert on conflict
## Returning
One can add * or specific into returning.
Returning can be added to delete, insert, update.
## Aggregates
- sqllite accepts printing column not present in group by.
- one can add something like count(*) filter (where sth = 'aaaa')
- sum/total -> sum for all empty columns will display null, total will display 0.0
- can load with extension decimal_sum
- group_concat and string_concat is something like listagg in oracle

## Sqlschema
``` 
select type,sql from sqlite_schema where tbl_name='<table name>';
```

## Schema version
One can use PRAGMA user_version to store schema version <- it is not used by sqlite itself.

## Backup
### All
sqlite3 database ".backup" 
### Excluding sparse data (marked as deleted)
sqlite3 database "vacuum into 'backup.db'"
## Dumb
sqlite3 database ".dump" > dump.sql -> export as series of sql commands

## JSON
[JSON / JSOB](json.md)

## Full text search
Sqlite has full text support (fts5). Be aware update, insert and delete from source table will not be populated automatically. One can rebuild fts table (strange syntax) or use triggers using:
- insert to react on insert
- insert with 'delete' to react on delete
- insert with 'delete' and insert to react to update

