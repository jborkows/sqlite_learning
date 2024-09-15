# JSON / JSOB
``` 
sqlite> select json_extract('{"foo":2, "bar":{"a":"AAA"}}','$.bar');
{"a":"AAA"}
sqlite> select jsonb_extract('{"foo":2, "bar":{"a":"AAA"}}','$.bar');
la7AAA
sqlite> select jsonb_extract('{"foo":2, "bar":{"a":"AAA"}}','$.bar.a');
AAA
sqlite>
```

## Arrays
``` 
sqlite> select json_object('example','[1,2,3]');
{"example":"[1,2,3]"} -- not an array
sqlite> select json_object('example',json('[1,2,3]'));
{"example":[1,2,3]} -- array
sqlite> select json_object('example','[1,2,3]'->'$');
{"example":[1,2,3]} -- array
sqlite> select json_object('example',json_array(1,2,3));
{"example":[1,2,3]} -- array
```   

## Json functions
``` 
sqlite> select json('{"a":1}');
{"a":1}
sqlite> select json_insert('{"a":1}', '$.b',2);
{"a":1,"b":2}
sqlite> select json_insert('{"a":1}', '$.a',2);
{"a":1} -- does not modify if exists
sqlite> select json_replace('{"a":1}', '$.a',2);
{"a":2} 
sqlite> select json_replace('{"a":1}', '$.b',2);
{"a":1} -- does not do anything if not exists
sqlite> select json_set('{"a":1}', '$.b',2);
{"a":1,"b":2} -- always overrides
sqlite> select json_set('{"a":1}', '$.a',2);
{"a":2}
sqlite> select json_remove('{"a":1}', '$.a');
{}
sqlite> select json_remove('{"a":[1,2,3]}', '$.a[1]'); --removes at index
{"a":[1,3]} 
sqlite>

sqlite> select json_patch('{"a":1,"b":2}','{"b":3,"c":4}');
{"a":1,"b":3,"c":4}
```

### json each
Even in cross join it is connected with row where json was e.g.:
``` 

sqlite> create table foo (name text primary key, tags text);
sqlite> insert into foo values ('a','["red","blue"]'),('b','["green", "yellow"]');
sqlite> .mode box
sqlite> select name, tags.value as tag from foo, json_each(foo.tags) as tags;
┌──────┬────────┐
│ name │  tag   │
├──────┼────────┤
│ a    │ red    │
│ a    │ blue   │
│ b    │ green  │
│ b    │ yellow │
└──────┴────────┘

```
