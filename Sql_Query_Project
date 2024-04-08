
--------------

-- Question 1: Create database and table for credit card transactions
CREATE DATABASE IF NOT EXISTS credit_card_transactions;
USE credit_card_transactions;

CREATE TABLE IF NOT EXISTS transactions (
    transaction_id INT,
    city VARCHAR(100),
    transaction_date DATE,
    card_type VARCHAR(50),
    exp_type VARCHAR(50),
    gender CHAR(1),
    amount DECIMAL(10, 2)
);


-- Question 2: Total Number of records
SELECT COUNT(*) AS total_records FROM transactions;

-- 26052

-- Question 3: Time line of the dataset
SELECT MIN(transaction_date) AS start_date, MAX(transaction_date) AS end_date FROM transactions;

-- '2013-10-04','2015-05-26'

-- Question 4: Unique card types
SELECT DISTINCT card_type FROM transactions;

-- 'Gold'
-- 'Platinum'
-- 'Silver'
-- 'Signature'

-- Question 5: Unique expense types
SELECT DISTINCT exp_type FROM transactions;

/* 
'Bills'
'Food'
'Entertainment'
'Grocery'
'Fuel'
'Travel' 
*/

-- Question 6: Distribution of transaction amounts across different card types
SELECT 
    card_type, 
    COUNT(*) AS transaction_count,
    AVG(amount) AS average_amount,
    MIN(amount) AS min_amount,
    MAX(amount) AS max_amount
FROM transactions
GROUP BY card_type;


-- 'Gold','6367','154631.62','1038.00','996754.00'
-- 'Platinum','6398','157492.81','1005.00','998077.00'
-- 'Silver','6840','156376.27','1018.00','955468.00'
-- 'Signature','6447','157133.72','1024.00','994537.00'


-- Question 7: Proportion of male and female transactions in the dataset
SELECT 
    gender, 
    COUNT(*) AS transaction_count,
    COUNT(*) / (SELECT COUNT(*) FROM transactions) AS proportion
FROM transactions
GROUP BY gender;

-- 'F','13680','0.5251'
-- 'M','12372','0.4749'


-- Question 8: Frequency of transactions over time
SELECT 
    YEAR(transaction_date) AS year,
    MONTH(transaction_date) AS month,
    COUNT(*) AS transaction_count
FROM transactions
GROUP BY YEAR(transaction_date), MONTH(transaction_date)
ORDER BY YEAR(transaction_date), MONTH(transaction_date);


-- '2013','10','1291'
-- '2013','11','1257'
-- '2013','12','1379'
-- '2014','1','1345'
-- '2014','2','1191'
-- '2014','3','1314'
-- '2014','4','1295'
-- '2014','5','1321'
-- '2014','6','1239'
-- '2014','7','1310'
-- '2014','8','1393'
-- '2014','9','1321'
-- '2014','10','1374'
-- '2014','11','1365'
-- '2014','12','1323'
-- '2015','1','1377'
-- '2015','2','1242'
-- '2015','3','1308'
-- '2015','4','1292'
-- '2015','5','1115'


-- Question 9: Identification of outliers in transaction amounts
SELECT *
FROM transactions
WHERE amount > (SELECT AVG(amount) + 3 * STDDEV(amount) FROM transactions)
   OR amount < (SELECT AVG(amount) - 3 * STDDEV(amount) FROM transactions);
   
   
-- Question 10: Most common expense types among customers
SELECT exp_type, COUNT(*) AS frequency
FROM transactions
GROUP BY exp_type
ORDER BY COUNT(*) DESC;

-- 'Food','5463'
-- 'Fuel','5257'
-- 'Bills','5078'
-- 'Entertainment','4762'
-- 'Grocery','4754'
-- 'Travel','738'

-- Question 11: Trends or patterns in transaction amounts over time
SELECT 
    YEAR(transaction_date) AS year,
    AVG(amount) AS average_amount
FROM transactions
GROUP BY YEAR(transaction_date)
ORDER BY YEAR(transaction_date);


-- '2013','156262.77'
-- '2014','155450.20'
-- '2015','158900.45'

-- Question 12: Overall expenditure pattern across different cities
SELECT city, SUM(amount) AS total_expenditure
FROM transactions
GROUP BY city
ORDER BY total_expenditure DESC;


-- Question 13: write a query to print top 5 cities with highest spends and their percentage contribution of total credit card spends

with cte1 as(
Select city, sum(amount) as total_spend
from transactions
group by city)
, total_spent as (Select sum(amount) as total_amount from transactions)

Select * , round(total_spend/total_amount,2) * 100 as percent_contribution from
cte1, total_spent
order by total_spend desc limit 5;

-- 'Greater Mumbai','576751476.00','4074833373.00','14.00'
-- 'Bengaluru','572326739.00','4074833373.00','14.00'
-- 'Ahmedabad','567794310.00','4074833373.00','14.00'
-- 'Delhi','556929212.00','4074833373.00','14.00'
-- 'Kolkata','115466943.00','4074833373.00','3.00'


-- Question 14: write a query to print highest spend month and amount spent in that month for each card type
Select * from transactions;
with cte as (
Select card_type, month(transaction_date) as Trans_mth, year(transaction_date) as trans_year, sum(amount) as high_spent
from transactions
group by card_type, month(transaction_date), year(transaction_date) )
-- order by  card_type,high_spent desc 
Select * from (Select * , rank() over(partition by card_type order by high_spent desc) as rn
from cte) a where rn=1;

-- 'Gold','1','2015','55455064.00','1'
-- 'Platinum','8','2014','57936507.00','1'
-- 'Signature','12','2013','58799522.00','1'
-- 'Silver','3','2015','59723549.00','1'

-- Question 15: write a query to print the transaction details(all columns from the table) for each card type when 
--    it reaches a cumulative of 1000000 total spends(We should have 4 rows in the o/p one for each card type)

with cte as (
select *,sum(amount) over(partition by card_type order by transaction_date,transaction_id) as total_spend
from transactions
-- order by card_type,total_spend desc
)
select * from (select *, rank() over(partition by card_type order by total_spend) as rn  
from cte where total_spend >= 1000000) a where rn=1;

-- '7565','Bengaluru','2013-10-04','Silver','Food','F','205179.00','1115582.00','1'
-- '1522','Delhi','2013-10-04','Gold','Food','M','281924.00','1272624.00','1'
-- '191','Ahmedabad','2013-10-05','Platinum','Bills','F','612572.00','1537482.00','1'
-- '73','Delhi','2013-10-04','Signature','Bills','F','550782.00','1285819.00','1'

-- Question 16: Write a query to find city which had lowest percentage spend for gold card type

with cte as (
select city,card_type,sum(amount) as amount
,sum(case when card_type='Gold' then amount end) as gold_amount
from transactions
group by city,card_type)
select 
city,sum(gold_amount)*1.0/sum(amount) as gold_ratio
from cte
group by city
having count(gold_amount) > 0 
order by gold_ratio limit 1;

-- 'Dhamtari','0.0033299'

-- Question 17: write a query to print 3 columns:  city, highest_expense_type, lowest_expense_type (example format: Delhi , bills, Fuel)
                
with cte as (
select city,exp_type, sum(amount) as total_amount from transactions
group by city,exp_type)
select
city , max(case when rn_asc=1 then exp_type end) as lowest_exp_type
, min(case when rn_desc=1 then exp_type end) as highest_exp_type
from
(select *
,rank() over(partition by city order by total_amount desc) rn_desc
,rank() over(partition by city order by total_amount asc) rn_asc
from cte) A
group by city;

-- Question 18: write a query to find the percentage contribution of spending by females for each expense type

select exp_type,
sum(case when gender='F' then amount else 0 end)*1.0/sum(amount) as percentage_female_contribution
from transactions
group by exp_type
order by percentage_female_contribution desc;

-- 'Bills','0.6394588'
-- 'Food','0.5490531'
-- 'Travel','0.5113287'
-- 'Grocery','0.5091102'
-- 'Fuel','0.4971038'
-- 'Entertainment','0.4937291'

-- Question 19: which card and expense type combination saw highest month-over-month growth in January 2014

with cte as (
select card_type,exp_type,year(transaction_date) yt
,month(transaction_date) mt,sum(amount) as total_spend
from transactions
group by card_type,exp_type,year(transaction_date),month(transaction_date)
)
select *, (total_spend-prev_mont_spend) as mom_growth
from (
select *
,lag(total_spend,1) over(partition by card_type,exp_type order by yt,mt) as prev_mont_spend
from cte) A
where prev_mont_spend is not null and yt=2014 and mt=1
order by mom_growth desc limit 1;

-- 'Platinum','Grocery','2014','1','12256343.00','7757562.00','4498781.00'

-- Question 20: during weekends which city has highest total spend to total no of transcations ratio (top 5)
 
select city, sum(amount)*1.0/count(1) as ratio
from transactions
where weekday(transaction_date) in (1,7)
-- where weekday(transaction_date) in ('Saturday','Sunday')
group by city
order by ratio desc limit 5;

-- 'Dalli-Rajhara','299906.0000000'
-- 'Lal Gopalganj Nindaura','299885.0000000'
-- 'Kavali','299176.0000000'
-- 'Kadapa','298654.0000000'
-- 'Uravakonda','298057.0000000'

-- Question 21: which city took least number of days to reach its
-- 500th transaction after the first transaction in that city;

with cte as (
select *
,row_number() over(partition by city order by transaction_date,transaction_id) as rn
from transactions)
select city,TIMESTAMPDIFF(DAY, MIN(transaction_date), MAX(transaction_date)) as datediff1
from cte
where rn=1 or rn=500
group by city
having count(1)=2
order by datediff1 limit 1; 

-- 'Bengaluru','81'
