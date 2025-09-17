# RFM-Analysis-Recency-Frequency-Monetary-Using-SQL
An e-commerce company wanted to better understand customer value and loyalty. The raw dataset included inconsistencies like null values and negative quantities. Using RFM (Recency, Frequency, Monetary) analysis, the goal was to clean the data, calculate key metrics, and segment customers to improve marketing, retention, and revenue strategies.

--

## Objectives / Analysis Performed
**1. Data Cleaning & Preparation

-Removed rows with NULL CustomerID values

Excluded records with negative quantities

Ensured dataset was analysis-ready

**2. RFM Metrics Calculation

Recency: Days since the customer’s last purchase

Frequency: Total number of distinct purchase transactions

Monetary: Total spend (Quantity × Unit Price)

**3. Customer Segmentation Using RFM Scores

Applied NTILE(5) ranking to divide Recency, Frequency, and Monetary into quintiles

Combined into a 3-digit RFM Score

Segmented customers into categories:

Champion

Loyal

Potential Loyalist

At Risk

Lost

**4. Additional Analysis

Identified customers at risk of churn using Recency scores

Measured revenue contribution by each RFM group

Created a MONTHS view to analyze monthly purchase trends
