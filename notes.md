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
