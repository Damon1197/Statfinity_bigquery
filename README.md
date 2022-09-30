# Statfinity_bigquery
Statfinity sql in big query 
#1
select Store_type,count(*) as COUNT  
from `statfinity_sql_case.Transaction`
group by Store_type order by 1 desc limit 1;
# 1 Answer : e-Shop is the Channel which is most frequently used for Transactions - 9311.

#2
select Gender, count(*) as COUNT 
from `statfinity_sql_case.customer`
where Gender in ("M","F") 
group by Gender;
#2 Answer: Count of male &amp; female   
#F:  2753
#M : 2892

#3
select city_code, count(*) as COUNT 
from `statfinity_sql_case.customer` 
group by city_code 
order by 2  desc 
limit 1 ;
#3 Answer: city_code= 3, count: 595

#4 
select prod_subcat 
from `statfinity_sql_case.prod_cat`
where prod_cat = "Books";
#4 Answer= Sub-categories of Books are:1.Fiction,2.Academic,3.Non-Fiction,4.Children,5. Comics,6.DIY


#5
select prod_cat, max(Qty) as MAX_Quantity 
from `statfinity_sql_case.transaction` as t
left join `statfinity_sql_case.prod_cat` as p on t.prod_cat_code = p.prod_cat_code
group by p.prod_cat;
#5 Answer:1)  prod_cat:Clothing, Max_qty=5 2) Home and kitchen,5 3)Books,5 4) Electronics,5 5)Bags,5 6)Footwear,5

#6
select sum(total_amt) as Net_Amount
from `statfinity_sql_case.transaction` as t
inner join `statfinity_sql_case.prod_cat` as p 
on t.prod_cat_code = p.prod_cat_code
and t.prod_subcat_code = p.prod_sub_cat_code
where p.prod_cat in ("Books","Electronic");
#6 Answer:  Net revenue generated in categories Books&#39;,&#39;Electronics are: 23545157.6749

#7
select cust_id,COUNT(cust_id) AS Count_of_Transaction
from `statfinity_sql_case.transaction`
where Qty >= 0
group by cust_id
having COUNT(cust_id) > 10;
#7 Answer:1.cust_id=270803 ,count_of_transactions=11,2.cust_id=266794 ,count_of_transactions=11,3.cust_id=273014 ,count_of_transactions=11,4.cust_id=270535 ,count_of_transactions=11,5.cust_id=272741 ,count_of_transactions=11,6.cust_id=274227, count_of_transactions=11


#8
select sum(total_amt) as Combine_Revnue
from `statfinity_sql_case.transaction` as t 
inner join `statfinity_sql_case.prod_cat` as p 
on t.prod_cat_code = p.prod_cat_code
and t.prod_subcat_code = p.prod_sub_cat_code
where prod_cat in ("Clothing","Electronics")
and Store_type = "Flagship store";
#8 Answer: The combined revenue earned From the Clothing&#39;,&#39;Electronics&#39;, &#39;Flagship store&#39; is = 3409559.2700000065


#9
select prod_subcat, sum(total_amt) as Revenu
from `statfinity_sql_case.transaction` as t
left join `statfinity_sql_case.customer` as c on t.cust_id = c.customer_Id
left join `statfinity_sql_case.prod_cat` as p 
on t.prod_subcat_code = p.prod_sub_cat_code
 and t.prod_subcat_code = p.prod_sub_cat_code
where p.prod_cat = "Electronics" and "Gender" = "M"
group by p.prod_subcat;
#9 Answer= 1)Mobiles ,4272912.3449999755 ,2 )Cameras, 1172702.245, 3)Audio and video, 3302504.660000002,4 )Personal Appliances, 1107593.4350000005,5)Computers,1417.3400

#10
select prod_subcat, (sum(total_amt)/(select sum(total_amt)
from `statfinity_sql_case.transaction`))*100 as Percantage_of_sales,
(count(case when Qty < 0 then Qty else null end)/sum(Qty))*100 as Percentage_of_return
from `statfinity_sql_case.transaction` as t
inner join `statfinity_sql_case.prod_cat` as p on t.prod_cat_code = p.prod_cat_code
and t.prod_subcat_code = p.prod_sub_cat_code
group by prod_subcat
order by sum(total_amt) desc 
limit 5 ;

#10 Answer:1) Mens,12.705601366439925,4.072913699800627,2)Women,12.694958469501122,4.41666666666667,3) Kids,8.76102622547202,3.6481556546412648 4)Mobiles,4.6288868012019941,3.2470042520293778 5)Fiction,4.5950201432576971,4.0031092110376987


#11
select cust_id,sum (total_amt) AS Revenu from `statfinity_sql_case.transaction`
where cust_id in 
(select customer_id from `statfinity_sql_case.customer` where date_diff(date "2022-09-17",DOB, YEAR) between 25 and 35)
and tran_date between ((select max(tran_date)-
30 from `statfinity_sql_case.transaction`)) and (select max(tran_date) from `statfinity_sql_case.transaction`)
group by cust_id;
#11 Answer: 1)cust_id=273174, Revenu=709.41...... 45)268402 ,1780.155,46)268444 ,0.0,47)273081 ,3000.075,48)275227 ,987.87,49)267656 ,0.0,50)267547 ,3391.24,


#12
select prod_cat, sum(case when total_amt < 0 then total_amt else 0 end) as total_return_vale 
from `statfinity_sql_case.transaction` as t inner join `statfinity_sql_case.prod_cat` as p 
on t.prod_cat_code = p.prod_cat_code group by p.prod_cat, tran_date having tran_date &gt;(select max(tran_date)-
90 from `statfinity_sql_case.transaction`) order by total_return_vale asc limit 1;
#12 Answer: Books category has the max values of returns in last 3  months of Transactions.=-64934.220000000008

#13
select  Store_type,sum(Qty) Qty_sold,
round(sum(total_amt),2) sales_amount
from `statfinity_sql_case.transaction`
group by Store_type
order by sales_amount desc, Qty_sold desc
limit 1;
#13 Answer:e-Shop Store type sells the max products: Qty sold=22763  sales amount=19824816.05

#14
select prod_cat, avg(total_amt) avg_revenue_of_prod
from `statfinity_sql_case.transaction` as t 
inner join `statfinity_sql_case.prod_cat` as p on t.prod_cat_code = p.prod_cat_code 
group by prod_cat
having avg(total_amt) &gt; (select avg(total_amt) from `statfinity_sql_case.transaction`);
#14 Answer: 1 )Clothing,2111.8707736486376 2)Books,2112.8182633052947 3)  Electronics,2189.1514158840414

#15
select prod_cat, prod_subcat, avg(total_amt) avg_revenue, sum(total_amt) total_revenue from
 `statfinity_sql_case.transaction` as t inner join `statfinity_sql_case.prod_cat` as p on t
.prod_cat_code = p.prod_cat_code and  t.prod_subcat_code = p.prod_sub_cat_code where p.prod_cat_code in (select prod_cat_code from `statfinity_sql_case.transaction` group by prod_cat_code order by sum(case when Qty >0 then Qty else 0 end) desc limit 5) 
group by p.prod_cat,p.prod_subcat 
order by p.prod_cat, p.prod_subcat;
#15 Answer:1)Books,Academic 2125.4852119958632,055344.1999999983.....21)Home and kitchen,Tools,2024.714453860646,2149882.475000002




















 
