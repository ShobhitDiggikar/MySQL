# MySQL
Welcome to the SQL Questions and Notes.

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
3. We start by selecting the **area name**  then we use the customer density formula. </br>
4. As the area size column is in bigint we have to change its format so we use **CAST** to change it into decimal >>> **CAST(s.area_size AS DECIMAL)** </br>
5. Then we use Density_rank and order in descending.</br>
6. We have to join both tables i.e. transaction records and stores in order to show the area name and corresponding customer density </br>
7. Then we **Group By** the entire result by area name and area size to show the result according to the area size and area name</br>
8. In the end we select the area name and customer density and use **WHERE** clause for the top 3 density ranks










