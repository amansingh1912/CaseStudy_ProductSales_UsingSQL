# Case Study on Product Sales Analysis Using SQL

## Overview:
I took this dataset from CodeBasics Challenge where there were several datasets regarding details of customer, product, monthly sales, gross_sales, etc.

## Questions from the dataset and their code including output:

### 1. Find distinct market where region is APAC and customer is Atliq Exclusive

Ans:

#### Code:

select distinct(market) from dim_customer
where customer = 'Atliq Exclusive' and region = 'APAC';

### Output:
![q1](https://user-images.githubusercontent.com/72240938/222162903-253f1949-51b5-4833-a408-325476597658.png)

### 2. Find unique product increase in fact_gross_price in 2020 vs 2021 including column names : unique_product_code_2020, unique_product_code_2021, percentage_chg
Ans:

### Code:
with t1 as
(SELECT count(distinct product_code) as unique_product_code_2020 from fact_gross_price
where fiscal_year = '2020'
),
t2 as
(
SELECT count(distinct product_code) as unique_product_code_2021 from fact_gross_price
where fiscal_year = '2021'
)
SELECT *, ROUND((
(unique_product_code_2021-unique_product_code_2020)* 100/unique_product_code_2020),2) as percentage_chg
from t1, t2;

### Output:

![q2](https://user-images.githubusercontent.com/72240938/222165472-6b2c82d5-1c15-4f81-b4dc-a1297d07c451.png)


### 3. Find unique product count for each segment including columns: segment, product_count
Ans:

### Code:
SELECT segment, count(distinct product_code) as Product_Count
from dim_product
group by segment
order by Product_Count desc;

### Output:

![q3](https://user-images.githubusercontent.com/72240938/222167890-e09421ee-61ef-4707-903f-e08b3fa8e9ec.png)


4. (i) Find the product, product_code with their manufacturing cost which has got the highest overall.
Ans:

### Code:
SELECT fm.product_code, d.product, SUM(fm.manufacturing_cost) as Highest_TotalManufacturingCost
from fact_manufacturing_cost fm
join dim_product d on fm.product_code = d.product_code
group by fm.product_code, d.product
order by Highest_TotalManufacturingCost desc
limit 1;

### Output:

![4 i](https://user-images.githubusercontent.com/72240938/222170044-c7060031-201f-47f9-8f06-ac9abf980135.png)

4. (ii) Find the product, product_code with their manufacturing cost which has got the least overall.
Ans:

### Code:

SELECT fm.product_code, d.product, SUM(fm.manufacturing_cost) as Highest_TotalManufacturingCost
from fact_manufacturing_cost fm
join dim_product d on fm.product_code = d.product_code
group by fm.product_code, d.product
order by Highest_TotalManufacturingCost asc
limit 1;

### Output:

![4 ii](https://user-images.githubusercontent.com/72240938/222170623-10232877-a503-4dd5-b265-ab6179bc69af.png)

5. Find the average discount percentage for each customer with their customer codes.
Ans:

### Code:
SELECT fp.customer_code, d.customer, ROUND(avg(pre_invoice_discount_pct),3) as AvgDiscountPct
from fact_pre_invoice_deductions fp
right join dim_customer d on fp.customer_code= d.customer_code
group by fp.customer_code, d.customer
order by AvgDiscountPct desc
limit 5;

### Output:

![5 no](https://user-images.githubusercontent.com/72240938/222171170-11644db3-b3f3-47dc-9c31-8dd71e507337.png)

6. In which quarter of year 2020, maximum total sold quantity was there.
Ans:

### Code:
with t1 as
(SELECT extract(year from date_reg) as Year, extract(Quarter from date_reg) as quarter, avg(sold_quantity) as AvgSoldQuantity  
from fact_sales_monthly
group by Year, quarter
order by AvgSoldQuantity desc
)
SELECT Year, quarter, ROUND(AvgSoldQuantity,2)
from t1 
where Year= '2020'
order by AvgSoldQuantity desc;

### Output:

![6 no](https://user-images.githubusercontent.com/72240938/222171833-3c3fc130-6f9e-443b-bd79-d54ab38b7619.png)

7. Find the top 3 customer codes who purchased the most for each product (if available)
Ans:

### Code:
SELECT * from (SELECT product_code, customer_code,
dense_rank() over(partition by product_code order by sold_quantity desc) as rnk
from fact_sales_monthly) x where
x.rnk<=3;

### Output:

![last no](https://user-images.githubusercontent.com/72240938/222174553-37eb394d-8fc6-4ebb-b969-b83303719795.png)










































































