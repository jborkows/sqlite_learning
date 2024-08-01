# Window functions
```sql
select 
*,
row_number() over (partition by user_id) as row_num_user,
first_value(id) over (partition by user_id order by created_at asc) as first_value
from bookmarks 
where user_id < 10;

```
Can also extract window
```sql
select 
*,
row_number() over (w_user) as row_num_user,
first_value(id) over (w_user order by created_at asc) as first_value
from bookmarks 
where user_id < 10
window
w_user as (partition by user_id); --can add here order by
```
## last value

```sql
select 
id, url,
row_number() over (w_user) as row_num_user,
first_value(id) over (w_user order by created_at asc) as first_value,
last_value(id) over (w_user order by created_at asc) as last_value
from bookmarks 
where user_id < 10
window
w_user as (partition by user_id); 
```
here last value look into value inside of window when processing of window happens, so last value fluctuate inside of window.

```sql
select 
id, url,
row_number() over (w_user) as row_num_user,
first_value(id) over (w_user order by created_at asc) as first_value,
first_value(id) over (w_user order by created_at desc) as last_value
from bookmarks 
where user_id < 10
window
w_user as (partition by user_id); 
```
## other functions
- lead -> next item inside of partition
- lag -> previous item inside of partition
