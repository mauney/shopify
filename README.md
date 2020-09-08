# Winter 2021 Data Science Intern Challenge #

Please complete the following questions, and provide your thought process/work. You can attach your work in a text file, link, etc. on the application page. Please ensure answers are easily visible for reviewers!

## Question 1: ##
Given some sample data, write a program to answer the following: [click here to access the required data set](https://docs.google.com/spreadsheets/d/16i38oonuX1y1g7C_UAmiK9GkY7cS-64DfiDMNiR41LM/edit#gid=0)

On Shopify, we have exactly 100 sneaker shops, and each of these shops sells only one model of shoe. We want to do some analysis of the average order value (AOV). When we look at orders data over a 30 day window, we naively calculate an AOV of $3145.13. Given that we know these shops are selling sneakers, a relatively affordable item, something seems wrong with our analysis. 

a. Think about what could be going wrong with our calculation. Think about a better way to evaluate this data.  
b. What metric would you report for this dataset?  
c. What is its value?  


### Average Order Value Higher Than Expected ###
Orders for March, 2017

A reasonable replacement metric for the average order value is the median order value.
Median Order Value: $284

Alternatively, an AOV calculated after removing outliers in item price or order quantity could be used.
Modified AOV: $300

The average, or mean, order value is not a good representation of a typical order for the 100 shops. The higher than expected average is influenced by a few factors:
 - Shop 78 has shoes priced at $25,725. This is probably incorrect. The high value is pulling up the value of the average order from other shops.
 - Shop 42 has 17 orders for 2,000 items, which are billed at $704,000 each. No other store has an order for more than 8 items. These orders have an outsize effect on the average order value.
 - Shop 42, in addition to filling large orders, has shoes priced at $352. The other 98 shops have items ranging from $90 to $201. This is a borderline case of an outlier compared to the previous two examples, but it is worth noting.

Medians are not influenced by a few extreme values, and in this case it is a good choice to represent a typical, or central, value.

Please see the [shopify_q1.ipynb](shopify_q1.ipynb) Jupyter Notebook for the analysis accompanying this question

## Question 2: ##
For this question you’ll need to use SQL. [Follow this link](https://www.w3schools.com/SQL/TRYSQL.ASP?FILENAME=TRYSQL_SELECT_ALL) to access the data set required for the challenge. Please use queries to answer the following questions. Paste your queries along with your final numerical answers below.

The indentions for SQL queries are not maintained in this markdown document. Please see the [shopify_q2.txt](shopify_q2.txt) file for queries that are easier to read.

### A. ###
How many orders were shipped by Speedy Express in total?  
54

SELECT COUNT(*) AS num_orders_shipped_Speedy_Express  
FROM Orders  
WHERE ShipperID = (SELECT DISTINCT ShipperID  
                   FROM Shippers  
                   WHERE ShipperName = "Speedy Express");  

Result:  
num_orders_shipped_Speedy_Express  
54

### B. ###
What is the last name of the employee with the most orders?  
Peacock  

SELECT LastName AS Employee_most_orders_lastname  
FROM Employees  
WHERE EmployeeID = (SELECT TOP 1 EmployeeID  
					FROM Orders  
					GROUP BY EmployeeID  
					ORDER BY COUNT(*) DESC);  

Result:  
Employee_most_orders_lastname  
Peacock  

### C. ###
What product was ordered the most by customers in Germany?  

Note: This question can be read at least two different ways. The first answer interprets 'the most' as referring to the number of units ordered. The second answer interprets 'the most' as referring to the number of orders in which a product appears. If appropriate, I would ask for clarification before submitting a response.   

*Answer One*  
Boston Crab Meat, with 160 units ordered in total from Germany  

*Answer Two*  
Gorgonzola Telino, appearing in five orders from Germany 

*Query One*  
SELECT TOP 1 ProductName AS Most_ordered_product_by_quantity_from_Germany,  
       SUM(Quantity) AS total_units  
FROM Products  
INNER JOIN OrderDetails  
ON Products.ProductID = OrderDetails.ProductID  
WHERE OrderID IN (SELECT OrderID  
				  FROM Orders  
                  WHERE CustomerID IN (SELECT CustomerID  
                                       FROM Customers  
                                       WHERE Country = "Germany"))  
GROUP BY ProductName  
ORDER BY SUM(Quantity) DESC;  

Result:  
Most_ordered_product_by_quantity_from_Germany  
Boston Crab Meat  
total_units  
160  

*Query Two*  
SELECT TOP 1 ProductName AS Most_ordered_product_by_order_from_Germany,  
       COUNT(*) AS number_of_orders  
FROM Products  
INNER JOIN OrderDetails  
ON Products.ProductID = OrderDetails.ProductID  
WHERE OrderID IN (SELECT OrderID  
				  FROM Orders  
                  WHERE CustomerID IN (SELECT CustomerID  
                                       FROM Customers  
                                       WHERE Country = "Germany"))  
GROUP BY ProductName  
ORDER BY COUNT(*) DESC;  

Result:  
Most_ordered_product_by_order_from_Germany  
Gorgonzola Telino  
number_of_orders  
5  
