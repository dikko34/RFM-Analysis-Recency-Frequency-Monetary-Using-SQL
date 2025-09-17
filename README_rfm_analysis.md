
# 📊 RFM Analysis (Recency, Frequency, Monetary) Using SQL

## 🧩 Problem Statement / Business Context
An e-commerce company wanted to better understand customer value and loyalty. The raw dataset included inconsistencies like null values and negative quantities. Using RFM (Recency, Frequency, Monetary) analysis, the goal was to clean the data, calculate key metrics, and segment customers to improve marketing, retention, and revenue strategies.

---

## 🎯 Objectives / Analysis Performed

**1. 📊 Data Cleaning & Preparation**  
- Removed rows with `NULL` CustomerID values  
- Excluded records with negative quantities  
- Ensured dataset was analysis-ready  

**2. 🔢 RFM Metrics Calculation**  
- **Recency**: Days since the customer’s last purchase  
- **Frequency**: Total number of distinct purchase transactions  
- **Monetary**: Total spend (Quantity × Unit Price)  

**3. 🏷️ Customer Segmentation Using RFM Scores**  
- Applied `NTILE(5)` ranking to divide Recency, Frequency, and Monetary into quintiles  
- Combined into a 3-digit `RFM Score`  
- Segmented customers into categories:  
  - `Champion`  
  - `Loyal`  
  - `Potential Loyalist`  
  - `At Risk`  
  - `Lost`  

**4. 📉 Additional Analysis**  
- Identified customers at risk of churn using Recency scores  
- Measured **revenue contribution** by each RFM group  
- Created a `MONTHS` view to analyze monthly purchase trends  

---

## 🗃️ Dataset Description
**Table:** `E-Commerce`  

**Key Columns:**
- CustomerID  
- InvoiceDate  
- InvoiceNo  
- Quantity, UnitPrice  
- TotalSpend (derived)  

---

## 🛠️ Tools & SQL Concepts Used
- SQL Server  
- Data filtering with `WHERE`  
- Window functions: `NTILE() OVER()`  
- Aggregations: `SUM()`, `COUNT(DISTINCT)`, `DATEDIFF()`  
- Views for reusability: `Customer_Segment`, `MONTHS`  
- Case logic for segment labeling  

---

## 🧠 Sample SQL Snippets

### 🔹 Calculating RFM Metrics
```sql
SELECT CustomerID,
       DATEDIFF(DAY, MAX(InvoiceDate), '2011-12-09') AS Recency_Of_Purchase,
       COUNT(DISTINCT InvoiceNo) AS Frequency,
       SUM(Quantity * UnitPrice) AS Monetary
FROM [E-Commerce]
WHERE CustomerID IS NOT NULL AND Quantity > 0
GROUP BY CustomerID;
```

### 🔹 Segmenting Customers with RFM Scores
```sql
WITH RFM AS (
    SELECT CustomerID,
           DATEDIFF(DAY, MAX(InvoiceDate), '2011-12-09') AS Recency_Of_Purchase,
           COUNT(DISTINCT InvoiceNo) AS Frequency,
           SUM(Quantity * UnitPrice) AS Monetary
    FROM [E-Commerce]
    WHERE CustomerID IS NOT NULL AND Quantity > 0
    GROUP BY CustomerID
), RFM_Score AS (
    SELECT *,
           NTILE(5) OVER (ORDER BY Recency_Of_Purchase DESC) AS R,
           NTILE(5) OVER (ORDER BY Frequency ASC) AS F,
           NTILE(5) OVER (ORDER BY Monetary ASC) AS M
    FROM RFM
)
SELECT CustomerID, R, F, M,
       CONCAT(R, F, M) AS RFM_Score,
       CASE
         WHEN RFM_Score <= 152 THEN 'Lost'
         WHEN RFM_Score <= 211 THEN 'At Risk'
         WHEN RFM_Score <= 321 THEN 'Potential Loyalist'
         WHEN RFM_Score <= 421 THEN 'Loyal'
         ELSE 'Champion'
       END AS Segment
FROM RFM_Score;
```

### 🔹 Revenue by RFM Group
```sql
WITH RFM AS (
    SELECT CustomerID,
           DATEDIFF(DAY, MAX(InvoiceDate), '2011-12-09') AS Recency_Of_Purchase,
           COUNT(DISTINCT InvoiceNo) AS Frequency,
           SUM(Quantity * UnitPrice) AS Monetary
    FROM [E-Commerce]
    WHERE CustomerID IS NOT NULL AND Quantity > 0
    GROUP BY CustomerID
), RFM_Score AS (
    SELECT *,
           NTILE(5) OVER (ORDER BY Recency_Of_Purchase DESC) AS R,
           NTILE(5) OVER (ORDER BY Frequency ASC) AS F,
           NTILE(5) OVER (ORDER BY Monetary ASC) AS M
    FROM RFM
)
SELECT (R + F + M) / 3 AS RFM_Group,
       SUM(Monetary) AS Total_Revenue,
       SUM(Monetary) / COUNT(CustomerID) AS Average_Revenue_Per_Customer
FROM RFM_Score
GROUP BY (R + F + M) / 3
ORDER BY Total_Revenue DESC;
```

---

## 📈 Insights / Outcomes  
- ✅ **Champions and Loyal Customers** contributed the highest revenue and showed strong repeat purchase patterns  
- ⚠️ **At Risk and Lost Customers** had long inactivity periods and lower frequency, signaling urgent re-engagement campaigns  
- 🧮 **Potential Loyalists** offered opportunities for targeted marketing to encourage loyalty  
- 📊 Revenue distribution analysis revealed significant concentration among high-value customer groups  
- 📅 Monthly view enabled seasonality analysis, highlighting peak and low demand periods  
