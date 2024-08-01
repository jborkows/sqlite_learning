# Nulls fun
```sql
select (1,2,3) = (1,2,3) as comparison, 'equal' as description
union all select (1,2,3) = (1,2,4) , 'not equal' 
union all select (1,2,3) = (1,null,3) , 'N/A due to null' 
union all select (1,2,3) = (1,null,4) , 'not equal even with null' ;

```

┌────────────┬────────────────────────────┐
│ comparison │        description         │
├────────────┼────────────────────────────┤
│ 1          │ 'equal'                    │
│ 0          │ 'not equal'                │
│ NULL       │ 'N/A due to null'          │
│ 0          │ 'not equal even with null' │
└────────────┴────────────────────────────┘
