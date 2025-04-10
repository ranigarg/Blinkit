# BlinkIT Grocery Dataset - MySQL Data Analysis
## This project involves cleaning, analyzing, and deriving insights from a Netflix Dataset using MySQL.

Objectives:* Data Cleaning: Handling missing values, duplicates, and incorrect data types.*
            * Exploratory Data Analysis (EDA): Understanding the dataset through statistical analysis and value.*
            * Data Standardization: Renaming columns and ensuring consistency in categorical values.*
            * Performance Optimization: Enhancing database performance with indexing and optimized queries.*
### Dataset Description
The dataset contains information about grocery items and their associated outlet details. Key columns include:

- **ItemFatContent**: Fat content of the item (e.g., Low Fat, Regular).
- **ItemIdentifier**: Unique identifier for the grocery item.
- **ItemType**: Category/type of the item (e.g., Dairy, Beverages).
- **OutletEstablishmentYear**: Year the outlet was established.
- **OutletIdentifier**: Unique identifier for the outlet.
- **OutletLocationType**: Location type of the outlet (e.g., Tier 1, Tier 2).
- **OutletSize**: Size of the outlet (e.g., Small, Medium, High).
- **OutletType**: Type of the outlet (e.g., Supermarket, Grocery Store).
- **ItemVisibility**: The visibility percentage of the item in the store.
- **ItemWeight**: Weight of the item.
- **TotalSales**: Total sales amount for the item.
- **Rating**: User rating for the item (0-5 scale).

#### Steps to be performed

1. Database and Table Setup : Create a table to store grocery data.
use blinkit;
CREATE TABLE GroceryDataNoPK (
    ItemFatContent VARCHAR(50),
    ItemIdentifier VARCHAR(50),
    ItemType VARCHAR(100),
    OutletEstablishmentYear INT,
    OutletIdentifier VARCHAR(50),
    OutletLocationType VARCHAR(50),
    OutletSize VARCHAR(50),
    OutletType VARCHAR(50),
    ItemVisibility FLOAT,
    ItemWeight FLOAT,
    TotalSales FLOAT,
    Rating FLOAT
);

-- Change the name of table
RENAME TABLE GroceryDataNoPK TO GroceryData;

2. Load Data from CSV File
LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/BlinkIT Grocery Data.csv'
INTO TABLE GroceryData
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS
(ItemFatContent, ItemIdentifier, ItemType, OutletEstablishmentYear, OutletIdentifier, OutletLocationType, OutletSize, OutletType, ItemVisibility, ItemWeight, TotalSales, Rating);

3. Data Validation & Integrity Checks

-- Check Table Structure
Describe Grocerydata;

-- Check Row Counts
SELECT COUNT(*) AS TotalRows FROM GroceryData;

4. Handle Missing Values

-- Find Missing Values
SELECT 
    SUM(CASE WHEN ItemFatContent IS NULL THEN 1 ELSE 0 END) AS ItemFatContent_Null,
    SUM(CASE WHEN ItemWeight IS NULL THEN 1 ELSE 0 END) AS ItemWeight_Null,
    SUM(CASE WHEN Rating IS NULL THEN 1 ELSE 0 END) AS Rating_Null
FROM GroceryData;

5. Finding and Removing dublicates

-- Find a dublicates
SELECT *, COUNT(*) AS DuplicateCount
FROM GroceryData
GROUP BY ItemFatContent, ItemIdentifier, ItemType, OutletEstablishmentYear, OutletIdentifier, OutletLocationType, OutletSize, OutletType, ItemVisibility, ItemWeight, TotalSales, Rating
HAVING COUNT(*) > 1;

-- Delete Duplicates
-- Create a New Table Without Duplicates
CREATE TABLE GroceryData_Clean AS 
SELECT DISTINCT * FROM GroceryData;

-- Drop the Old Table
DROP TABLE GroceryData;

-- Rename the New Table
ALTER TABLE GroceryData_Clean RENAME TO GroceryData;

-- Verfity delete dublicates
SELECT COUNT(*) AS total_rows FROM GroceryData;

-- Standardize Item Fat Content
SET SQL_SAFE_UPDATES = 0;
UPDATE GroceryData
SET ItemFatContent = 
    CASE 
        WHEN ItemFatContent IN ('low fat', 'LF') THEN 'Low Fat'
        WHEN ItemFatContent = 'reg' THEN 'Regular'
        ELSE ItemFatContent 
    END;
SET SQL_SAFE_UPDATES = 1;


✅ Data is cleaned and standardized
✅ No missing values in key columns
✅ Duplicates removed

6. Statistical Analysis of BlinkIT Grocery Dataset
Summary Statistics for Numerical Columns
SELECT 
    MIN(TotalSales) AS Min_SellingPrice, 
    MAX(TotalSales) AS Max_SellingPrice, 
    AVG(TotalSales) AS Avg_SellingPrice, 
    STDDEV(TotalSales) AS Std_SellingPrice,
    MIN(Rating) AS Min_Rating, 
    MAX(Rating) AS Max_Rating, 
    AVG(Rating) AS Avg_Rating, 
    STDDEV(Rating) AS Std_Rating
FROM GroceryData;

7."Insights of these Dataset"
1. Top 10 Best-Selling Products by total sales.
2. Category-wise Sales to determine which product categories perform best.
3. Sales Performance by Outlet Type to see which type of store generates the most revenue.
4. Impact of Outlet Size on Sales to compare small, medium, and large outlets.
5. Sales Trends by Establishment Year to check if newer outlets perform better.
6. Most Profitable Location Type based on total revenue.
7. Correlation between Visibility & Sales to see if more visible products sell better.
8. Average Sales per Product Type to understand which items sell well consistently.
9. Sales Distribution across Item Fat Content (Regular vs. Low Fat).
10. Sales Performance by Rating to see if higher-rated products sell more.
11. Price Variation (Min, Max, Avg) Across Categories.
12. Standard Deviation of Sales to check which items have consistent sales.
13. Top 5 Outlets with Highest Sales.
14. Bottom 5 Outlets with Lowest Sales.
15. Outlets with Highest Customer Ratings.

1. Top 10 Best-Selling Products by total sales.
SELECT ItemIdentifier, ItemType, SUM(TotalSales) AS Total_Revenue
FROM GroceryData
GROUP BY ItemIdentifier, ItemType
ORDER BY Total_Revenue DESC
LIMIT 10;

2. Category-wise Sales to determine which product categories perform best.
SELECT ItemType, SUM(TotalSales) AS Total_Revenue
FROM GroceryData
GROUP BY ItemType
ORDER BY Total_Revenue DESC;

3. Sales Performance by Outlet Type
SELECT OutletType, SUM(TotalSales) AS Total_Revenue
FROM GroceryData
GROUP BY OutletType
ORDER BY Total_Revenue DESC;

4. Impact of Outlet Size on Sales
SELECT OutletSize, SUM(TotalSales) AS Total_Revenue
FROM GroceryData
GROUP BY OutletSize
ORDER BY Total_Revenue DESC;

5. Sales Trends by Establishment Year
SELECT OutletEstablishmentYear, SUM(TotalSales) AS Total_Revenue
FROM GroceryData
GROUP BY OutletEstablishmentYear
ORDER BY OutletEstablishmentYear;

6. Most Profitable Location Type
SELECT OutletLocationType, SUM(TotalSales) AS Total_Revenue
FROM GroceryData
GROUP BY OutletLocationType
ORDER BY Total_Revenue DESC;

7. Correlation between Visibility & Sales
SELECT 
    CASE 
        WHEN ItemVisibility < 0.02 THEN 'Low Visibility'
        WHEN ItemVisibility BETWEEN 0.02 AND 0.05 THEN 'Medium Visibility'
        ELSE 'High Visibility'
    END AS VisibilityCategory,
    AVG(TotalSales) AS Avg_Sales
FROM GroceryData
GROUP BY VisibilityCategory;

8. Average Sales per Product Type
SELECT ItemType, AVG(TotalSales) AS Avg_Sales
FROM GroceryData
GROUP BY ItemType
ORDER BY Avg_Sales DESC;

9. Sales Distribution across Item Fat Content
SELECT ItemFatContent, SUM(TotalSales) AS Total_Revenue
FROM GroceryData
GROUP BY ItemFatContent
ORDER BY Total_Revenue DESC;

10. Sales Performance by Rating
SELECT Rating, AVG(TotalSales) AS Avg_Sales
FROM GroceryData
GROUP BY Rating
ORDER BY Rating DESC;

11. Price Variation (Min, Max, Avg) Across Categories
SELECT ItemType, MIN(TotalSales) AS Min_Sales, MAX(TotalSales) AS Max_Sales, AVG(TotalSales) AS Avg_Sales
FROM GroceryData
GROUP BY ItemType
ORDER BY Avg_Sales DESC;

12. Standard Deviation of Sales (Variability of Sales)
SELECT ItemType, STDDEV(TotalSales) AS Sales_Variability
FROM GroceryData
GROUP BY ItemType
ORDER BY Sales_Variability DESC;

13. Top 5 Outlets with Highest Sales
SELECT OutletIdentifier, SUM(TotalSales) AS Total_Revenue
FROM GroceryData
GROUP BY OutletIdentifier
ORDER BY Total_Revenue DESC
LIMIT 5;

14. Bottom 5 Outlets with Lowest Sales
SELECT OutletIdentifier, SUM(TotalSales) AS Total_Revenue
FROM GroceryData
GROUP BY OutletIdentifier
ORDER BY Total_Revenue ASC
LIMIT 5;

15. Outlets with Highest Customer Ratings
SELECT OutletIdentifier, AVG(Rating) AS Avg_Rating
FROM GroceryData
GROUP BY OutletIdentifier
ORDER BY Avg_Rating DESC
LIMIT 5;

## Final Summary :
Bestsellers: Baking Goods & Household Items
📌 Top Categories: Frozen Foods, Snacks, Fruits & Vegetables
📌 Best Outlets: Large Supermarkets in Tier-1 Locations
📌 Sales Drivers: Visibility, Customer Ratings, Outlet Type
📌 Optimization Areas: Price Variability, Low-Performing Outlets, Demand Forecasting.
