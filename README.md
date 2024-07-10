.headers on <- adds headers
.mode changes way of display
.mode json
.mode markdown
.separator "," <- changes separator in list mode to ,
.mode qbox <- display like markdown
.mode insert <- generates insert
.shell clear <- executes shell script
.dbconfig

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


select * from generate_series(0,100)

## flexible types
```sql 
create table ex2(a integer, b text);
insert into ex2 values(1,'a');
insert into ex2 values('a',2); --this works!
select typeof(a) from ex2; --shows integer,text type is cell not in column
 insert into ex2 values('2','2'); 
```
Now '2' is converted in integer:

```sql 
select typeof(a), typeof(b), a, b from ex2;
```
| typeof(a) | typeof(b) | a | b |
|-----------|-----------|---|---|
| integer   | text      | 1 | a |
| text      | text      | a | 2 |
| integer   | text      | 2 | 2 |

## sqlite types
- null
- integer
- real
- text
- blob

### Type affinity
- Types which names contains "INT" -> integer
- Types which names contains "CHAR", "CLOB", "TEXT" -> text
- Types "blob" or none -> blob
- Types "REAL", "FLOA", "DOUB" -> real

e.g.
```sql 
 create table aaa(first myint, aaa varchar, b, c doublet);
 insert into aaa values(1,1,1,1);
 .mode markdown
 select typeof(first),typeof(aaa), typeof(b),typeof(c) from aaa;
```
| typeof(first) | typeof(aaa) | typeof(b) | typeof(c) |
|---------------|-------------|-----------|-----------|
| integer       | text        | integer   | real      |


### Script types
```sql
create table ex(int integer, text text) strict;
insert into ex values(1,'aa'); --works
insert into ex values(1,1); --works
insert into ex values('a','1'); --error
```
#### mixed
```sql

create table foo(key text, value any) strict; -- ket strongly typed, value "flexible"
```

### Date
```sql
select date(); --ISO date -> text
select datetime();
select time();

select julianday(); -- real
select unixepoch('subsec'); -- real

select strftime('%d - %m', 'now'); --text
select timediff(date(),date());--text

select datetime('now', 'start of month', '+1 month', '-1 day'); -- will show last day of month
select datetime(1718138004, 'unixepoch', 'start of month');
select date('now', 'start of year', '+10 month', 'weekday 4', '+21 days');

select julianday()-julianday('2022-12-17'); --difference in days
```
### booleans
```sql
create table aaa(bool int);
insert into aaa values(false);
insert into aaa values(true);
.mode markdown
select * from aaa;
.mode markdown
select * from aaa where bool=true;

```

| bool |
|------|
| 0    |
| 1    |

| bool |
|------|
| 1    |
