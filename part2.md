# PART2 - 빅데이터 분석 (07/18)

#### 1) 
![user_training](/images/pt2_1.png)
![user_training](/images/pt2_1_1.png)

```
use problem1;

select a.id,
       a.type,
       a.status,
       a.amount,
       (a.amount - b.avg_amount) as difference
from account as a,
(select avg(amount) as avg_amount from account) as b
where a.status='Active';

```

#### 2) 
![user_training](/images/pt2_2.png)
![user_training](/images/pt2_2_1.png)

#### 3) 
![user_training](/images/pt2_3_1.png)
![user_training](/images/pt2_3_2.png)

#### 4) 
![user_training](/images/pt2_4_1.png)
![user_training](/images/pt2_4_2.png)
![user_training](/images/pt2_4_3.png)

#### 5) 
![user_training](/images/pt2_5.png)
![user_training](/images/pt2_5_n.png)

```
use problem5;

select fname, lname, city, state from employee
  where city ='Palo Alto'
    and state ='CA'
union all
select fname, lname, city, state from customer
  where city ='Palo Alto'
    and state ='CA';

```

#### 6) 
![user_training](/images/pt2_6_1.png)
![user_training](/images/pt2_6_2.png)

#### 7) 
![user_training](/images/pt2_7.png)
![user_training](/images/pr2_7_n.png)

```
use problem7;

SELECT * FROM
(
  select concat(fname,' ',lname) as name
    from employee
    where city='Seattle'
) as emp
order by emp.name asc;
```


#### 8) 
![user_training](/images/pt2_8_1.png)
![user_training](/images/pt2_8_2.png)

#### 9) 
![user_training](/images/pt2_9_1.png)

#### 10) 
![user_training](/images/pt2_10_1.png)
![user_training](/images/pt2_10_2.png)

#### 11) 
![user_training](/images/pt2_11_1.png)
![user_training](/images/pt2_11_2.png)
![user_training](/images/pt2_11_3.png)
