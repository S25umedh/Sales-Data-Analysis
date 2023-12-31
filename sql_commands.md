Q1: What were the total sales for each month in 2005?

select distinct YEAR_ID as 'Year', month_id as 'Month', sum(SALES) as 'Total Sales'
from purchase
where YEAR_ID = 2005
group by YEAR_ID, MONTH_ID
order by sum(SALES) desc;


Q2: Which customers generated the top 10 highest total sales in the last quarter of 2003?

select c.CUSTOMER_NAME as 'Customer Name', sum(p.SALES) as 'Total Sales' 
from purchase p
join customer c on c.order_number = p.order_number
where p.QTR_ID = 4 and p.YEAR_ID = 2003
group by c.CUSTOMER_NAME
order by sum(p.SALES) desc
limit 10;


Q3: How much revenue was generated by each product category in the current year (2005)?

select distinct pr.PRODUCT_CODE as 'Product', sum(p.SALES) as 'Revenue'
from purchase p
join product pr on pr.order_number = p.order_number
where YEAR_ID = 2005
group by pr.PRODUCT_CODE
order by sum(p.SALES) desc;


Q4: What is the average order value for the last six months?

select avg(price_each) as 'Average Order Value' from purchase
where order_date >= date_sub('2005-05-31', interval 6 month) and order_date <= '2005-05-31';


Q5: How many customers made repeat purchases within the last 90 days?

select count(distinct c.CUSTOMER_NAME) as 'No. of Customers'
from purchase p
join customer c on c.order_number = p.order_number
where p.ORDER_DATE >= date_sub('2005-05-31', interval 90 day) and p.order_date <= '2005-05-31';


Q6: How have the sales of product S10_4698 changed month-over-month in the last year?

select pr.PRODUCT_CODE as 'Product', p.month_id as 'Month', sum(p.SALES) as 'Total Sales'
from purchase p
join product pr on pr.order_number = p.order_number
where pr.PRODUCT_CODE = 'S10_4698' and p.YEAR_ID  = 2004
group by pr.PRODUCT_CODE, p.MONTH_ID
order by sum(p.SALES) desc; 


Q7: What were the total sales for each region in the last quarter?

select distinct c.territory as 'Region', sum(p.SALES) as 'Total Sales'
from purchase p
join customer c on c.order_number = p.order_number
where p.QTR_ID = 1 and p.YEAR_ID = 2005 ##last date being 2005-05-31##
group by c.territory;


Q8: Which three products had the highest sales in terms of quantity sold last month?

select pr.product_code as 'Products', p.order_date 'Date', sum(p.SALES) 'Total Sales'
from purchase p
join product pr on pr.order_number = p.order_number
where p.order_date >= '2005-05-01'
group by pr.product_code, p.order_date
order by sum(p.sales) desc
limit 3;


Q9: Which product category has shown the most growth in sales over the last two years?

select pr.product_code, sum(p.sales) as 'Total Sales', (max(p.year_id)) - 2 as 'Start Year', (max(p.year_id)) as 'End Year' 
from purchase p
join product pr on pr.order_number = p.order_number
where p.order_date between date_sub('2005-05-31', interval 2 year) and '2005-05-31'
group by pr.PRODUCT_CODE
order by 'Total Sales'
Limit 1;


Q10: Which regions have consistently outperformed in terms of sales over the last year?

select c.territory as 'Region', sum(p.sales) as 'Total Sales'
from purchase p
join customer c on c.order_number = p.order_number
where p.order_date between date_sub('2005-05-31', interval 2 year) and '2005-05-31'
group by c.TERRITORY
order by 'Total Sales' desc
limit 3;


Q11: Which customer service agents have handled the high deal size and what is the total sale amount?

select distinct a.CONTACT_FIRST_NAME 'Agent Name', sum(p.SALES) 'Total Sales'
from purchase p 
join agent a on p.order_number = a.order_number
where a.dealsize = 'Large' 
group by a.CONTACT_FIRST_NAME
order by sum(p.SALES) desc;


Q12: Create a list of agent names by concatenating first and last names.

select concat(contact_last_name, '  ', contact_first_name) from agent;


Q13: Create a sales category where the range of sales amount is categorized and above 11051.

select order_number, order_date, status, sales,
case 
	when sales between 0 and 5000 then 'Low sales'
    when sales between 5001 and 10000 then 'Moderate sales'
    when sales between 10001 and 15000 then 'High sales'
    end 'Sales Category'
    from purchase
    where sales > 11051
    order by 1;
    

Q14:  What percentage of customers churned (stopped purchasing) in the last six months?

#Number of customers purchased before 6 months
select  'A' as 'Before', count(distinct c.customer_name) as 'TotalCustomersBefore'
from purchase p
join customer c on c.ORDER_NUMBER = p.ORDER_NUMBER
where p.ORDER_DATE < date_sub('2005-05-31', interval 6 month);


#Number of customers purchased during 6 months
select 'A' as 'Before', count(distinct c.customer_name) as 'TotalCustomersDuring'
from purchase p
join customer c on c.ORDER_NUMBER = p.ORDER_NUMBER
where p.ORDER_DATE >= date_sub('2005-05-31', interval 6 month) and p.ORDER_DATE <= '2005-05-31';


select ((TotalCustomersBefore - TotalCustomersDuring)*100/TotalCustomersBefore) as 'Churn Percentage'
from
(select  'A' as 'Before', count(distinct c.customer_name) as 'TotalCustomersBefore'
from purchase p
join customer c on c.ORDER_NUMBER = p.ORDER_NUMBER
where p.ORDER_DATE < date_sub('2005-05-31', interval 6 month)) a
join (select 'A' as 'Before', count(distinct c.customer_name) as 'TotalCustomersDuring'
from purchase p
join customer c on c.ORDER_NUMBER = p.ORDER_NUMBER
where p.ORDER_DATE >= date_sub('2005-05-31', interval 6 month) and p.ORDER_DATE <= '2005-05-31') b on a.Before = b.before;


Q15: Calculate the number of products with 'Car' in its name.

select PRODUCT_LINE, count(PRODUCT_LINE) from product
where PRODUCT_LINE regexp 'car'
group by PRODUCT_LINE;


Q16: Calculate daily sales fluctuations to compare each day's sales with the previous day.

select order_number, sum(sales), order_date, lag(sum(SALES)) over (order by date(order_date)) as 'Previous', sum(sales) - lag(sum(sales)) over (order by date(order_date)) as 'Change'
from purchase
group by order_number, order_date;


Q17: Display the count of sales orders by status (e.g., Pending, Shipped, Delivered).

select distinct status as 'Status', count(sales) as 'Total Sales'
from purchase
group by status
order by count(sales) desc;

