## **Project: Business Performance Analysis**

## **Overview**

In this project, I will use SQL to analyze business performance by looking at:

1. **Sales Metrics Analysis** – To track trends, measure growth, and identify top-performing products and regions.
2. **Customer Segmentation** – To segment customers based on demographics, behavior, and purchase history.

This type of analysis can help companies improve sales performance, target high-value customers, and optimize marketing efforts.

### **Data Source**

I are assuming the data is stored in the following tables:
- **sales_data**: This table contains transaction data.
- **customer_data**: This table contains customer demographic data.
- **product_data**: This table contains product details.
- **region_data**: This table contains region-based data.

#### **sales_data Table**
| order_id | product_id | customer_id | order_value | order_date  | region    |
|----------|------------|-------------|-------------|-------------|-----------|
| 1001     | P001       | C001        | 250.00      | 2023-05-01  | East      |
| 1002     | P002       | C002        | 150.00      | 2023-05-02  | West      |
| 1003     | P003       | C003        | 300.00      | 2023-05-03  | North     |
| 1004     | P004       | C001        | 400.00      | 2023-05-04  | South     |
| 1005     | P005       | C004        | 500.00      | 2023-05-05  | East      |

#### **customer_data Table**
| customer_id | name       | age | gender | city       | loyalty_status |
|-------------|------------|-----|--------|------------|----------------|
| C001        | Alice      | 30  | F      | New York   | Platinum       |
| C002        | Bob        | 45  | M      | Los Angeles| Gold           |
| C003        | Carol      | 40  | F      | Chicago    | Silver         |
| C004        | Dave       | 28  | M      | Boston     | Bronze         |

#### **product_data Table**
| product_id | product_name | category      | price |
|------------|--------------|---------------|-------|
| P001       | Widget A     | Electronics   | 100   |
| P002       | Widget B     | Home Appliances| 150  |
| P003       | Widget C     | Electronics   | 200   |
| P004       | Widget D     | Furniture     | 250   |
| P005       | Widget E     | Electronics   | 300   |

#### **region_data Table**
| region  | manager         | target_sales |
|---------|-----------------|--------------|
| East    | John Doe        | 50000        |
| West    | Jane Smith      | 45000        |
| North   | Mary Johnson    | 30000        |
| South   | James Brown     | 35000        |

---

## **Key Tasks**

1. **Sales Metrics Analysis**  
   - Calculate total sales for each region.
   - Calculate the growth in sales over time (Month-to-month or Year-over-year).
   - Identify the top-selling products by region and total sales.

2. **Customer Segmentation**  
   - Segment customers based on their loyalty status.
   - Calculate the average order value by customer demographic (e.g., age, gender).
   - Analyze purchase frequency by customer type.

---

## **Step-by-Step Implementation**

---

### **1. Sales Metrics Analysis**

#### **Total Sales by Region**

To see how sales perform across different regions, I aggregate the `sales_data` by region and calculate the total sales per region.

```sql
-- Calculate total sales by region
SELECT 
    region,
    SUM(order_value) AS total_sales
FROM sales_data
GROUP BY region
ORDER BY total_sales DESC;
```

#### **Sales Growth (Month-to-Month)**

To calculate the growth in sales month-over-month, I first group the data by month and year. Then I calculate the sales for each month and find the growth percentage.

```sql
-- Sales growth month-to-month
WITH monthly_sales AS (
  SELECT
    TO_CHAR(order_date, 'YYYY-MM') AS month,
    SUM(order_value) AS total_sales
  FROM sales_data
  GROUP BY TO_CHAR(order_date, 'YYYY-MM')
)
SELECT 
    month,
    total_sales,
    LAG(total_sales) OVER (ORDER BY month) AS previous_month_sales,
    (total_sales - LAG(total_sales) OVER (ORDER BY month)) / LAG(total_sales) OVER (ORDER BY month) * 100 AS sales_growth_percentage
FROM monthly_sales
ORDER BY month DESC;
```

#### **Top Products by Total Sales**

To identify the top-selling products, I aggregate the `sales_data` by `product_id` and calculate the total sales for each product.

```sql
-- Identify top-selling products
SELECT 
    p.product_name,
    SUM(s.order_value) AS total_sales
FROM sales_data s
JOIN product_data p ON s.product_id = p.product_id
GROUP BY p.product_name
ORDER BY total_sales DESC
LIMIT 10;
```

---

### **2. Customer Segmentation**

#### **Customer Segmentation by Loyalty Status**

Segment customers based on their loyalty status (e.g., Platinum, Gold, Silver, Bronze). I can calculate the total spend for each loyalty status group.

```sql
-- Segment customers by loyalty status
SELECT
    c.loyalty_status,
    SUM(s.order_value) AS total_sales,
    COUNT(DISTINCT c.customer_id) AS total_customers
FROM sales_data s
JOIN customer_data c ON s.customer_id = c.customer_id
GROUP BY c.loyalty_status
ORDER BY total_sales DESC;
```

#### **Average Order Value by Age Group**

Segment customers based on their age and calculate the average order value for each group.

```sql
-- Calculate the average order value by age group
SELECT
    CASE 
        WHEN c.age < 30 THEN 'Under 30'
        WHEN c.age BETWEEN 30 AND 40 THEN '30-40'
        WHEN c.age BETWEEN 41 AND 50 THEN '41-50'
        ELSE 'Over 50'
    END AS age_group,
    AVG(s.order_value) AS average_order_value
FROM sales_data s
JOIN customer_data c ON s.customer_id = c.customer_id
GROUP BY age_group
ORDER BY average_order_value DESC;
```

#### **Purchase Frequency by Loyalty Status**

Measure how often customers make a purchase based on their loyalty status.

```sql
-- Measure purchase frequency by loyalty status
SELECT
    c.loyalty_status,
    COUNT(s.order_id) AS purchase_count,
    COUNT(DISTINCT s.customer_id) AS unique_customers,
    COUNT(s.order_id) / COUNT(DISTINCT s.customer_id) AS avg_purchases_per_customer
FROM sales_data s
JOIN customer_data c ON s.customer_id = c.customer_id
GROUP BY c.loyalty_status
ORDER BY avg_purchases_per_customer DESC;
```

---

## **Reporting & Dashboards**

### **Visualizations**

Once you have the data, you can create visualizations to report the findings:
- **Sales by Region** – A bar chart showing total sales by region.
- **Sales Growth** – Line chart showing month-over-month sales growth.
- **Top Products** – Bar chart for top-selling products.
- **Customer Segmentation** – Pie chart showing the distribution of loyalty status.
- **Average Order Value by Age Group** – Bar chart showing average order value across different age groups.
- **Purchase Frequency by Loyalty Status** – Bar chart showing purchase frequency by loyalty group.

You can use **Tableau**, **Power BI**, or **Matplotlib** (Python) to create these dashboards.

---

### **Further Improvements**
- **Forecasting**: Integrate time-series