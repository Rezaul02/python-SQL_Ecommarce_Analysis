## E-commerce Data Analysis Using Python and MySQL
Overview
This project demonstrates how to connect to a MySQL database using Python to perform various data analysis tasks related to e-commerce data. The code covers a range of queries and visualizations, including identifying unique customer locations, counting orders, calculating sales by category, and more.

### Prerequisites

Before running the script, ensure you have the following installed:

- Python 3.x
- MySQL server
- Required Python libraries: `pandas`, `matplotlib`, `seaborn`, `mysql-connector-python`
### Code Breakdown
#### 1. Connecting to the MySQL Database


import pandas as pd 
import matplotlib.pyplot as plt 
import seaborn as sns 
import mysql.connector 
DB = mysql.connector.connect(
    host="localhost",
    username="ecomarce",
    password="123", 
    database="ecomarce"
)
cur = DB.cursor()
This section establishes a connection to the MySQL database using the `mysql.connector` library. The connection details (`host`, `username`, `password`, `database`) are provided as arguments. A cursor object `cur` is then created to execute SQL queries.

#### 2. Listing All Unique Customer Cities

query = """ select distinct customer_city from customers """
cur.execute(query)
data = cur.fetchall()
df = pd.DataFrame(data)
df
This query retrieves all unique customer cities from the `customers` table. The results are stored in a DataFrame `df` for easy manipulation and display.

#### 3. Counting the Number of Unique Customer Cities


query = """ select count(distinct customer_city) from customers """
cur.execute(query)
data = cur.fetchall()
data
This query counts the total number of unique customer cities in the `customers` table.

#### 4. Counting Orders Placed in 2017

```python
query = """ select count(order_id) from orders where year(order_purchase_timestamp)=2017 """
cur.execute(query)
data = cur.fetchall()
data[0][0]
```

This query counts the total number of orders placed in the year 2017.

#### 5. **Calculating Total Sales Per Category**


query = """ select products.product_category category, round(sum(payments.payment_value), 2)
from products 
join order_items on products.product_id  = order_items.product_id 
join payments on payments.order_id = order_items.order_id 
group by category """
cur.execute(query)
data = cur.fetchall()
data[0][0]

![a1](https://github.com/user-attachments/assets/7e8a04fb-ca5d-4a51-ba6e-41cfe92ebce1)

This query calculates the total sales for each product category. It joins the `products`, `order_items`, and `payments` tables to aggregate the sales by category.

### 6. Calculating the Percentage of Orders Paid in Installments
query = """ select (sum(case when payment_installments >=1  then 1 else 0 end))/count(*)*100 from payments """
cur.execute(query)
data = cur.fetchall()
print("the percentage of orders that were paid in installments", data[0][0])


This query calculates the percentage of orders that were paid in installments, based on the `payment_installments` field in the `payments` table.

#### 7. Counting Customers from Each State

query = """ SELECT customer_state, count(customer_id) FROM customers GROUP BY customer_state """
cur.execute(query)
data = cur.fetchall()
df = pd.DataFrame(data, columns=['state', 'country_count'])
df = df.sort_values(by="country_count", ascending=False)
df


This query counts the number of customers from each state, grouping the results by `customer_state`. The result is displayed in descending order based on the customer count.


plt.bar(df['state'], df['country_count'])
plt.xticks(rotation=90)
plt.show()

A bar chart is then plotted to visualize the number of customers from each state.

#### 8. Calculating Monthly Orders in 2018


query = """ SELECT monthname(order_purchase_timestamp) months, count(order_id) 
from orders where year(order_purchase_timestamp)=2018 
group by months """
cur.execute(query)
data = cur.fetchall()
df = pd.DataFrame(data, columns=["months", "count_of_order"])

This query calculates the number of orders placed each month in 2018.

![a2](https://github.com/user-attachments/assets/cb7b536c-8018-47f3-9386-51c5f25a7cc5)


o = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"]
ax = sns.barplot(x=df['months'], y=df['count_of_order'], order=o, palette="viridis")
ax.bar_label(ax.containers[0])
plt.xticks(rotation=90)
plt.show()

The results are visualized using a bar plot, with the months ordered correctly.

#### 9. Finding Average Number of Products per Order by City


query = """ 
WITH count_per_order AS (
    SELECT 
        orders.order_id, 
        orders.customer_id, 
        COUNT(order_items.order_id) AS order_count
    FROM 
        orders 
    JOIN 
        order_items ON orders.order_id = order_items.order_id
    GROUP BY 
        orders.order_id, 
        orders.customer_id  
)
SELECT 
    customers.customer_city, 
    AVG(count_per_order.order_count)
FROM  
    customers 
JOIN 
    count_per_order ON customers.customer_id = count_per_order.customer_id 
GROUP BY 
    customers.customer_city;
"""
cur.execute(query)
data = cur.fetchall()
data


This query calculates the average number of products per order, grouped by customer city. A common table expression (CTE) named `count_per_order` is used to count the number of items per order, and the results are then averaged by city.

#### 10. Calculating Moving Average of Order Values


query = """ SELECT orders.order_id, orders.order_purchase_timestamp, payments.payment_value
FROM payments JOIN orders
ON payments.order_id = orders.order_id """
cur.execute(query)
data = cur.fetchall()
data

This query retrieves order IDs, timestamps, and payment values. This data can be used to calculate the moving average of order values for each customer over their order history.

### Conclusion

This script provides a comprehensive analysis of e-commerce data, utilizing Python and MySQL to extract valuable insights. The code can be expanded or modified to suit additional analytical needs, making it a flexible solution for e-commerce data analysis.

--- 

This explanation should provide a thorough understanding for anyone reading your GitHub README file.

