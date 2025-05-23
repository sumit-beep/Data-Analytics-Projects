# Supply Chain Optimization
## Background:
A company operates in the retail industry and sources products from various suppliers to stock
its inventory. The company aims to optimize its supply chain management process to ensure
efficient order fulfillment, minimize stockouts, and reduce carrying costs. The database contains
information about suppliers, products, orders, and shipments, which can be leveraged to
improve supply chain operations.
## Objectives:
- Analyze supplier performance and identify opportunities for collaboration.
- Optimize inventory levels to reduce stockouts and improve customer satisfaction.
- Streamline order fulfillment processes to enhance efficiency and reduce costs.
- Identify key trends and patterns in order and shipment data for strategic
decision-making.
## Database Schema:
The database schema includes the following tables:
- **Suppliers Table:** Stores information about suppliers, including supplier ID, name,
contact person, phone number, and email.

![image](https://github.com/user-attachments/assets/03c0bb75-0557-4a2b-b041-bb4de1b39468)
- **Products:** Contains details about products, such as product ID, name, description, unit
price, and quantity in stock.

![image](https://github.com/user-attachments/assets/e980dec9-a643-4b11-b717-2aa6c5d71742)
- **Orders:** Captures data related to customer orders, including order ID, product ID,
supplier ID, order date, quantity ordered, and order status.

![image](https://github.com/user-attachments/assets/81bf546a-ac4a-4292-8b71-513c9d1a5c8a)
- **Shipments:** Stores information about shipments, including shipment ID, order ID,
shipment date, delivery date, shipping company, and tracking number.

![image](https://github.com/user-attachments/assets/2d901c35-8779-42e8-92a2-90df322d65f6)
## Analysis Objectives:
### 1. Supplier Performance Analysis:
- Analyze the total revenue generated by each supplier to identify top-performing
suppliers.
- Evaluate the average delivery time for shipments from different suppliers to assess
logistics efficiency.
- Identify suppliers with declining order trends or inconsistent performance over time for
further investigation and potential collaboration opportunities.
### 2. Inventory Optimization:
- Review inventory levels for each product and identify items with low stock levels (e.g.,
less than 50 units).
- Implement inventory forecasting models to predict demand and ensure optimal stock
levels to meet customer demand without excessive inventory holding costs.
- Establish reordering thresholds and automate replenishment processes to maintain
adequate inventory levels while minimizing stockouts.
### 3. Order Fulfillment Process Improvement:
- Analyze order status data to identify bottlenecks in the order fulfillment process.
- Evaluate the average shipment duration for each supplier and shipping company to
identify opportunities for streamlining logistics operations.
- Implement performance metrics to track order processing times and enhance operational
efficiency.
### 4. Trend Analysis and Strategic Insights:
- Conduct trend analysis to identify seasonal demand patterns and product popularity
trends.
- Identify correlations between order frequency, product categories, and customer
demographics to tailor inventory management strategies.
- Leverage historical order and shipment data to forecast future demand and optimize
procurement and logistics strategies accordingly.
## SQL Analysis
1. Select all records from the Suppliers table.
2. Select product name and unit price from the Products table
3. Select order IDs and order dates from the Orders table
4. Select shipment IDs and shipment dates from the Shipments table
5. Count the total number of products in stock
6. Calculate the average unit price of products
7. Find the maximum quantity ordered
8. List suppliers along with their contact persons
9. List products with their descriptions
10. Display shipment details including the tracking number
11. List orders along with the associated supplier information
12. Display products that have a unit price greater than $15
13. Count the number of orders per supplier
14. Calculate the total quantity ordered for each product
15. List shipments along with the associated order information
16. Find suppliers with more than 2 contacts
17. Calculate the average quantity ordered per order
18. List products along with the total number of orders they are associated with.
19. Display orders that are in progress (order_status = 'In Progress')
20. Find the earliest and latest order dates
21. Calculate the total revenue generated from orders
22. List suppliers along with the total quantity ordered from them
23. Find products with the highest unit price
24. List orders along with the associated supplier and product information
25. Display the top 3 products with the highest quantity ordered
26. Find the percentage of completed orders out of total orders
27. Calculate the total number of shipments per shipping company
28. List suppliers who have not yet made any orders
29. Display orders along with the corresponding shipment details, if available.
30. Find the top 5 suppliers with the highest total quantity ordered.
31. Find the total revenue generated by each supplier
32. Calculate the average delivery time for each shipping company.
33. Identify products that have never been ordered
34. Find the top 3 shipping companies with the most shipments
35. Calculate the percentage of orders that were completed for each supplier.
36. Identify products with low inventory levels (less than 50 in stock) that need restocking.
37. Find the top 5 suppliers with the highest total revenue.
38. Calculate the total number of orders made each month.
39. Identify suppliers with declining order trends over the past three months.
40. Calculate the average shipment duration for each supplier.
41. Identify Seasonal Demand Patterns.
42. Product Popularity Trends.
43. Correlation between Order Frequency, Product Categories, and Customer
Demographics.
44. Forecast Future Demand.
















