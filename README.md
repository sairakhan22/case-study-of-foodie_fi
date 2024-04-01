# case-study-of-foodie_fi
case study of subscription base business. Dany realizes that there is a huge gap. He wanted to created a new streaming which will only contain food related content.Danny finds a few smart friends to launch his new startup Foodie-Fi in 2020 and started selling monthly and annual subscriptions.

SELECT * FROM foodie_fi.plans;



select 
p.plan_name, s.start_date, s.customer_id

from foodie_fi.subscriptions s 

JOIN foodie_fi.plans p ON s.plan_id = p.plan_id;


1.How many customers has Foodie-Fi ever had?

1)SELECT COUNT(DISTINCT customer_id) AS total_customers
FROM foodie_pie.subscriptions;


2. What is the monthly distribution of trial plan start_date values for our dataset - use the start 
of the month as the group by value.
2)SELECT 
    DATE_FORMAT(start_date, '%Y-%m-01') AS Starting_Month,
    COUNT(plan_id) AS trial_count
FROM
    foodie_pie.subscriptions
WHERE
    plan_id = 0
GROUP BY DATE_FORMAT(start_date, '%Y-%m-01')
ORDER BY Starting_Month;


3)What plan ‘start_date’ values occur after the year 2020 for our dataset? Show the breakdown by count of events 
for each ‘plan_name’.

SELECT 
    p.plan_name, count(*) as event_count
FROM
    foodie_fi.subscriptions s
       JOIN
    foodie_fi.plans p on s.plan_id = p.plan_id
WHERE
    extract(year from start_date) > 2020
GROUP BY p.plan_name;

4)4. What is the customer count and percentage of customers who have churned rounded to 1
decimal place?

SELECT * from foodie_fi.plans;
SELECT count(distinct(customer_id)) as "Customer Count", 
ROUND(COUNT(DISTINCT customer_id) * 100.0 / (SELECT count(distinct customer_id) FROM foodie_fi.subscriptions), 1) AS "Percentage"
FROM foodie_fi.subscriptions where foodie_fi.plan_id = 4;



5)How many customers have churned straight after their initial free trial - what percentage is
this rounded to the nearest whole number?

 COUNT(distinct customer_id) as Customer_churn,
    ROUND(COUNT(customer_id) / (SELECT 
                    COUNT(distinct customer_id)
                FROM
                    foodie_fi.subscriptions) * 100) AS Churn_Percentage_Whole_no
FROM
    foodie_fi.subscriptions
WHERE
    foodie_fi.plan_id = 4 AND DAY(start_date) <= 8;


6)What is the number and percentage of customer plans after their initial free trial?
select 
    count(distinct customer_id) as total_customers,
    round(count(distinct s.customer_id) * 100.0 / (select 
                    count(distinct customer_id)
                from
                    foodie_fi.subscriptions
                where
                    plan_id != 0),
            1) as plan_percentage
from
    foodie_fi.subscriptions s
where
    s.plan_id != 0; 
   

7)What is the customer count and percentage breakdown of all 5 plan_name values at 2020-
12-31?
SELECT 
    p.plan_name,
    COUNT(s.subscription_id) AS customer_count,
    ROUND((COUNT(s.subscription_id) * 100.0) / total_subscriptions.total_count, 2) AS percentage
FROM 
    foodie_fi.subscriptions s
INNER JOIN 
    foodie_fi.plans p ON s.plan_id = p.plan_id
INNER JOIN 
    (SELECT COUNT(*) AS total_count FROM foodie_fi.subscriptions WHERE start_date <= '2020-12-31') AS total_subscriptions
WHERE 
    s.start_date <= '2020-12-31'
GROUP BY 
    p.plan_name;

8) How many customers have upgraded to an annual plan in 2020?

select 
    count(distinct customer_id) as upgraded_to_annual
from
    foodie_fi.subscriptions
where
    plan_id = 3 and year(start_date) = 2020;

9)How many days on average does it take for a customer to an annual plan from the day they
join Foodie-Fi?

SELECT COUNT(*) AS upgraded_customers_count
FROM subscriptions AS s
JOIN plans AS p ON p.plan_id = s.plan_id
WHERE
    p.plan_name = 'pro annual' 
    AND EXTRACT(YEAR FROM s.start_date) = 2020;

10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60
days etc)

SELECT 
    CASE
        WHEN DATEDIFF(end_date, start_date) BETWEEN 0 AND 30 THEN '0-30 days'
        WHEN DATEDIFF(end_date, start_date) BETWEEN 31 AND 60 THEN '31-60 days'
     
        ELSE '> 60 days'
    END AS period,
    COUNT(*) AS number_of_customers
FROM 
    foodie_fi.subscriptions
WHERE 
    plan_id IN (SELECT plan_id FROM foodie_fi.plans WHERE plan_interval = 'annual')
GROUP BY 
    period;

11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?

SELECT COUNT(*) AS downgrade_count
FROM foodie_fi.subscriptions
WHERE plan_id IN (SELECT plan_id FROM foodie_fi.plans WHERE plan_name = 'Pro Monthly')
AND start_date >= '2020-01-01' AND end_date <= '2020-12-31'
AND customer_id IN (
    SELECT customer_id
    FROM foodie_fi.subscriptions
    WHERE plan_id IN (SELECT plan_id FROM foodie_fi.plans WHERE plan_name = 'Basic Monthly')
);

