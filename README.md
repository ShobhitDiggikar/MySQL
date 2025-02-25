


# MySQL
Welcome to the SQL Questions and Notes.

## Calculate the sales revenue for the year 2021.</br>
<img width="502" alt="Screenshot 2025-01-02 at 10 33 14 PM" src="https://github.com/user-attachments/assets/68eba065-0075-4024-b348-73f9e8924108" /> </br>

### Explanation: The entire revenue was much higher than expected answer then I noticed, it is asking for only 2021 year. so used where & between




# Find the average difference between booking and checking dates  
Q. Find the average number of days between the booking and check-in dates for AirBnB hosts. Order the results based on the average number of days in descending order.
avg_days_between_booking_and_checkin DESC</br>

table: airbnb_contacts
<img width="1327" alt="Screenshot 2024-12-09 at 11 08 04 AM" src="https://github.com/user-attachments/assets/2586f608-a6c2-42b8-aebd-336b5282597e">
### Query 
SELECT
     id_host,</br>
     avg(DATEDIFF(ds_checkin, ts_booking_at)) AS avg_days_between_booking_and_checkin</br>
FROM airbnb_contacts</br>
GROUP BY 
     id_host</br>
HAVING 
     avg(DATEDIFF(ds_checkin, ts_booking_at)) >= 0</br>
ORDER BY 
     avg_days_between_booking_and_checkin DESC</br>

### **Solution breakdown**
1. __SELECT__: We select host_id as asked, Since both columns have different formats, we will use the 'DATEDIFF' clause and then the column name 1 and column name 2
2. __GROUP BY__: for each host we use host_id
3. __HAVING__: Once used GROUP BY we can't use WHERE, we can only use HAVING since it is used for GROUP and not each record
4. __ORDER BY__: To order the avg_days in descending order 

---
# CASE WHEN
To better understand the effect of the review count on the price of accomodation, categorize the number of reviews into the following groups along with the price.  
0 reviews: NO</br>
1 to 5 reviews: FEW</br>
6 to 15 reviews: SOME</br>
16 to 40 reviews: MANY</br>
more than 40 reviews: A LOT</br>
Output the price and it's categorization. Perform the categorization on accomodation level.

### Query
<img width="612" alt="Screenshot 2024-12-10 at 10 57 09 AM" src="https://github.com/user-attachments/assets/98610197-f41f-4720-97e8-93c41e01a837">

### Expected output 

<img width="679" alt="Screenshot 2024-12-10 at 10 58 30 AM" src="https://github.com/user-attachments/assets/8e15dfd8-e03e-4fee-a32a-a3c2c81ec24d">

### Note:</br> 
1. Remember to use the END or ELSE to finish the CASE statement
---

Calculate the total weight for each shipment and add it as a new column. Your output needs to have all the existing rows and columns in addition to the  new column that shows the total weight for each shipment. One shipment can have multiple rows.</br>

### solution: </br>
WITH cte AS </br>
(</br>
  select *, SUM(weight) AS total_weight </br>
  from amazon_shipment a</br>
  GROUP BY shipment_id </br>
)</br>
select amazon_shipment.*, total_weight</br>
from amazon_shipment</br>
JOIN cte ON amazon_shipment.shipment_id = cte.shipment_id</br>

### Explanation </br>

We created a cte first for calculating total_weight and creating a new column of it. Then we GROUPED BY shipment ID so each shipment can be seen, but we wanted all the rows even if they have occurred multiple times. </br>
To see each and every row ( even if they are repeated) shipment_id we had to join the tables so we joined both tables.</br>
We did INNER JOIN so all the matching rows get showed</br>


<img width="855" alt="Screenshot 2024-12-19 at 11 04 04 AM" src="https://github.com/user-attachments/assets/1a284cec-fd6c-4ed4-a70f-f2d534e82d5b" />



### If it Were a LEFT or RIGHT JOIN:</br>
#### LEFT JOIN: All rows from amazon_shipment are included, even if no match is found in cte. Non-matching rows will have NULL values for cte columns.</br>
#### RIGHT JOIN: All rows from cte are included, even if no match is found in amazon_shipment. Non-matching rows will have NULL values for amazon_shipment columns.</br>

---
### High Density Areas ( **hard** )
Identify the top 3 areas with the highest customer density. Customer density = (total number of unique customers in the area / area size).</br>

Your output should include the area name and its calculated customer density, and ties will be ranked the same.<be>
### Query </br>

<img width="841" alt="Screenshot 2024-12-22 at 5 26 41 PM" src="https://github.com/user-attachments/assets/5cead8d1-16ec-4ad6-bfbe-0faf288bd8a5" /> </br> 

WITH ranked_data AS
  (SELECT s.area_name,
          COUNT(DISTINCT t.customer_id) / CAST(s.area_size AS DECIMAL) AS customer_density,
          RANK() OVER (
                       ORDER BY COUNT(DISTINCT t.customer_id) / CAST(s.area_size AS DECIMAL) DESC) AS density_rank
   FROM transaction_records AS t
   JOIN stores AS s ON t.store_id = s.store_id
   GROUP BY s.area_name,
            s.area_size)
SELECT area_name,
       customer_density
FROM ranked_data
WHERE density_rank <= 3;

### Explanation
1. We have to find the top 3 areas with the highest customer density for which we have a formula </br>
2. But we have to start by creating CTE because the customer density column does not exist by default so we create it</br>
3. We start by selecting the **area name**  then we use the customer density formula. To count each unique customer we use **COUNT(DISTINCT** </br>
4. As the area size column is in bigint we have to change its format so we use **CAST** to change it into decimal >>> **CAST(s.area_size AS DECIMAL)** </br>
5. Then we use Density_rank and order in descending.</br>
6. We have to join both tables i.e. transaction records and stores in order to show the area name and corresponding customer density </br>
7. Then we **Group By** the entire result by area name and area size to show the result according to the area size and area name</br>
8. In the end we select the area name and customer density and use **WHERE** clause for the top 3 density ranks

---
# Find the second-highest salary in each department.

### table: employee_data</br>
<img width="931" alt="Screenshot 2024-12-25 at 3 37 15 PM" src="https://github.com/user-attachments/assets/7528bba3-9d8d-4d36-833f-e154488dd073" /> </br>

### Query
<img width="745" alt="Screenshot 2024-12-28 at 3 03 14 PM" src="https://github.com/user-attachments/assets/27379bd5-7085-45a4-8d6e-b97abd49e996" />


WITH ranked_salaries AS</br>
  (SELECT department,</br>
          employee_id,</br>
          salary,</br>
          DENSE_RANK() OVER (PARTITION BY department</br>
                             ORDER BY salary DESC) AS salary_rank</br>
   FROM employee_data)</br>
SELECT department,</br>
       employee_id,</br>
       salary AS second_highest_salary</br>
FROM ranked_salaries</br>
WHERE salary_rank = 2;</br> 

### solution: </br>
1. First we create a CTE named ranked_salaries to give ranks and we partition by department and 
   order salary in descending order </br>
2. Then we select department, employee_id and salary AS the second highest salary from 
   ranked_salaries (CTE)
3. Then we use the WHERE clause to select salary_rank = 2, this will give us the second-highest- 
   ranked salary in each department.

   ---
 ### Question  
<img width="612" alt="Screenshot 2025-01-08 at 10 34 46 PM" src="https://github.com/user-attachments/assets/9ca57d2e-5c3c-4558-bf7d-d17260ca52a2" /> </br>

### Sample Table

<img width="1659" alt="Screenshot 2025-01-08 at 10 37 00 PM" src="https://github.com/user-attachments/assets/1f90b000-6d54-476a-bf34-a516b464ad9c" />

### Solution

<img width="529" alt="Screenshot 2025-01-08 at 10 43 51 PM" src="https://github.com/user-attachments/assets/79287747-1d6d-48e7-a222-bcc57a66c903" />

---

## Easy

<img width="842" alt="Screenshot 2025-01-11 at 11 03 51 PM" src="https://github.com/user-attachments/assets/722e5ab2-72ab-47ee-8230-4d8564383994" />


<img width="485" alt="Screenshot 2025-01-11 at 11 10 27 PM" src="https://github.com/user-attachments/assets/7f7be6f3-d647-4ed5-a9d9-ce839fcdfaea" />

---
### Question
Write a solution to find the daily active user count for a period of 30 days ending 2019-07-27 inclusively. A user was active on someday if they made at least one activity on that day.</br>

### Query

<img width="538" alt="Screenshot 2025-01-16 at 6 35 41 PM" src="https://github.com/user-attachments/assets/7df08ff7-8293-430f-a3c3-98186ceeb013" />

### Output 

<img width="538" alt="Screenshot 2025-01-16 at 6 36 51 PM" src="https://github.com/user-attachments/assets/81251471-2e81-42e0-9b35-901afb3490dd" />

---
## Medium

<img width="844" alt="Screenshot 2025-01-16 at 7 05 09 PM" src="https://github.com/user-attachments/assets/f28e3480-7245-42e2-a1c1-7b3c94e5f8cd" />

data tables
<img width="1099" alt="Screenshot 2025-01-16 at 7 45 08 PM" src="https://github.com/user-attachments/assets/32e9471b-ded7-4e86-a6d9-6fb5fe4316c0" />


### Solution

SELECT u.user_id, DATE(u.signup_date) as signup_dtae, COUNT( a.activity_type ) AS activity_type</br>
FROM user_activities a</br>
JOIN user_profiles u ON a.user_id = u.user_id </br>
WHERE a.activity_date BETWEEN u.signup_date AND DATE_ADD(u.signup_date, INTERVAL 30 DAY) </br>
GROUP BY a.user_id, u.signup_date </br>

---
### Question

<img width="708" alt="Screenshot 2025-01-17 at 5 56 37 PM" src="https://github.com/user-attachments/assets/7b7a179c-8f31-4c79-bbaa-c6df16722e64" />

### Solution

SELECT id_guest, </br>
       DENSE_RANK() OVER(ORDE BY SUM(n_messages) DESC) AS ranks,  </br>
       SUM(n_messages) AS total_messages </br>
FROM airbnb_contacts </br>
GROUP BY id_guest, </br>
ORDER BY total_messages DESC; </br>

***Note: Remember to use the things with aggregation inside the window function*** </br>

### Output

<img width="858" alt="Screenshot 2025-01-17 at 6 02 25 PM" src="https://github.com/user-attachments/assets/63e44ccf-99c1-4b0e-87f2-b1e9f8bc2861" /> </br>


---
### Question

<img width="755" alt="Screenshot 2025-01-21 at 10 14 46 PM" src="https://github.com/user-attachments/assets/b0e3cba9-08ab-4b4e-93f0-a17c98e13da7" /> </br>

### data table

<img width="835" alt="Screenshot 2025-01-21 at 10 18 44 PM" src="https://github.com/user-attachments/assets/2852ee5c-417a-4491-9ab3-9df5b9fc21b6" /> </br>

### Query </br>

<img width="835" alt="Screenshot 2025-01-21 at 10 20 43 PM" src="https://github.com/user-attachments/assets/5d862b96-a74b-4435-9e2f-816e195ed9ad" />

### Explanation 
1. We have to find the total number of users and the number of active users, so we use >>> COUNT(user_id) AS total_users
2. To find the number of active_users we use CASE WHEN not WHERE. >>> COUNT(CASE WHEN status = 'open' THEN 1 ELSE NULL END) AS active_users
3. then to narrow down the users from the USA only >>> WHERE country = 'USA' for the USA active users share 
4. We make this entire query as a subquery so we get these results first and from here we use total_users and active_users to find the share.
5. Then we run the main query >>> SELECT active_users/CAST(total_users AS FLOAT) AS active_users_share and

---
### Question 

<img width="836" alt="Screenshot 2025-01-24 at 4 43 49 PM" src="https://github.com/user-attachments/assets/a922810e-5988-494d-b8d1-f21dc73991ff" />

### Solution

<img width="676" alt="Screenshot 2025-01-24 at 4 44 27 PM" src="https://github.com/user-attachments/assets/771df945-fb31-4f15-aded-087e322bbb02" />

---
  
Needs to be added something as gajanan keeps moving forward with the SQL it has motivated me as well to the SQL 
       

<img width="1656" alt="Screenshot 2025-02-25 at 9 31 15 AM" src="https://github.com/user-attachments/assets/d614ff7d-eedf-4923-9b66-229d3e8b5d55" />



















