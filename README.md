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






