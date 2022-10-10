# Week 1 Project Summary

How many users do we have? 
> 130
```sql
select count(user_id)
from stg_users;
```

On average, how many orders do we receive per hour? 
> 7.52
```sql
with orders_per_hour as (
    select trunc(created_at, 'hour') as order_hour, count(order_id) as num_orders
    from stg_orders
    group by 1
)

select round(avg(num_orders),2) as avg_orders_per_hour
from orders_per_hour;
```

On average, how long does an order take from being placed to being delivered? 
> 93.4 hours or 3.89 days
```sql
select round(avg(datediff('hour', created_at, delivered_at)),2) as avg_delivery_hours,
round(avg(datediff('day', created_at, delivered_at)),2) as avg_delivery_days
from stg_orders;
```

How many users have only made one purchase? 
> 25 
Two purchases? 
> 28 
Three+ purchases? 
> 71
```sql
with user_purchases as (
    select user_id, count(order_id) as num_orders
    from stg_orders 
    group by 1
)

select case when num_orders > 2 then '3 or more' else num_orders::string end as num_orders_binned, count(user_id) as num_users
from user_purchases
group by 1
order by 1 desc;
```

On average, how many unique sessions do we have per hour? 
> 16.33
```sql
with sessions_per_hour as (
    select trunc(created_at, 'hour') as session_hour, count(distinct session_id) as num_sessions
    from stg_events
    group by 1
)
select round(avg(num_sessions),2) as avg_sessions_per_hour
from sessions_per_hour;
```
