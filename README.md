# Python-Project
Supplement Sales analysis with Python  

This project explores and analyzes weekly supplement sales data using Python and Pandas. It's part of a portfolio aimed at showcasing practical data analysis skills, including data cleaning, feature engineering, and exploratory insights generation.

## Project Structure

- `supplement_sales.ipynb`: Main script for loading and preprocessing the sales dataset.
- `README.md`: Documentation and project overview.

## 📊 Dataset Overview

- **Source**: kaggle dataset
- **Frequency**: Weekly (Mondays only)
- **Columns**:
  - `Date`, `Product Name`, `Category`, `Units Sold`, `Price`, `Revenue`, `Discount`, `Units Returned`, `Location`, `Platform`

## Features & Processing

- Parses `Date` into datetime
- Adds `Year` and `DayOfWeek` columns for temporal analysis
- Provides summary statistics for revenue, sales, and returns
- Prepares the dataset for further exploration (e.g., trend analysis, seasonality, discount impact)

## Key Insights 

- **Top Categories by Revenue**: Vitamin, Mineral, Performance
- **Top Products by Revenue**: Biotin, Zinc, Pre-Workout
- **Price and Revenue Correlation**: Strong positive correlation between price and revenue
- **Units Returned**: Generally low, but slight correlation with units sold

## Potential Extensions

- Create visualizations (bar charts, line plots, heatmaps)
- Analyze trends over time
- Study the effect of discounts on revenue and returns
- Build a dashboard using tools like Tableau or Power BI


# 📊 Supplement Sales Weekly Analysis with Python

Exploratory data analysis on supplement sales. Includes data inspection, cleaning, feature engineering, and business insights based on weekly product-level transactions.

```python
import numpy as np  
import pandas as pd  
import matplotlib.pyplot as plt  
import seaborn as sns  

# Load dataset
file_path = "/Users/vernesapodrimaj/Downloads/Supplement_Sales_Weekly_Expanded.csv"
supplement_sales = pd.read_csv(file_path)

# Basic exploration
print(supplement_sales.shape)
print(supplement_sales.head())
print(supplement_sales.columns)
print("Duplicates:", supplement_sales.duplicated().sum())
print("Missing values:", supplement_sales.isnull().sum().sum())
print("Unique locations:", supplement_sales['Location'].unique())

# Convert and extract time features
supplement_sales['Date'] = pd.to_datetime(supplement_sales['Date'])
supplement_sales['Year'] = supplement_sales['Date'].dt.year
supplement_sales['DayOfWeek'] = supplement_sales['Date'].dt.day_name()

# Category analysis
category_counts = supplement_sales['Category'].value_counts()
product_counts = supplement_sales['Product Name'].value_counts()

# Sample slice
print(supplement_sales.iloc[20:41])

# Visualizations
sns.countplot(data=supplement_sales, y='Category', order=category_counts.index)
plt.title("Category Distribution")
plt.tight_layout()
plt.show()

sns.scatterplot(data=supplement_sales, x='Price', y='Revenue', hue='Category')
plt.title("Price vs Revenue")
plt.tight_layout()
plt.show()

# Correlation
corr = supplement_sales[['Units Sold', 'Revenue', 'Discount', 'Units Returned', 'Price']].corr()
sns.heatmap(corr, annot=True, cmap="coolwarm")
plt.title("Correlation Matrix")
plt.tight_layout()
plt.show()

# Units returned
print("Total Units Returned:", supplement_sales["Units Returned"].sum())

# Avg price by product
sns.barplot(x='Product Name', y='Price', data=supplement_sales)
plt.xticks(rotation=45)
plt.title('Average Price per Product')
plt.tight_layout()
plt.show()

# Heatmap: Units sold by location and category
heatmap_data = supplement_sales.pivot_table(values='Units Sold', index='Location', columns='Category', aggfunc='sum')
sns.heatmap(heatmap_data, annot=True, cmap='Blues', fmt='g')
plt.title('Units Sold by Location and Category')
plt.tight_layout()
plt.show()

# Lineplot: Units sold by platform over years
year_platform_sales = supplement_sales.groupby(['Year', 'Platform'])['Units Sold'].sum().reset_index()
sns.lineplot(data=year_platform_sales, x='Year', y='Units Sold', hue='Platform', marker='o')
plt.title('Units Sold by Platform Over the Years')
plt.tight_layout()
plt.show()

# Group by day of week and product
supplement_sales['Day of Week Name'] = pd.Categorical(
    supplement_sales['Date'].dt.day_name(),
    categories=['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'],
    ordered=True
)
day_product_sales = supplement_sales.groupby(['Day of Week Name', 'Product Name'])['Units Sold'].sum().reset_index()
print(day_product_sales.head(15))

# Top 10 products by revenue
totals = supplement_sales.groupby('Product Name').agg({'Units Sold': 'sum', 'Revenue': 'sum'}).reset_index()
top10 = totals.sort_values('Revenue', ascending=False).head(10)
sns.barplot(data=top10, x='Revenue', y='Product Name', palette='viridis')
plt.title('Top 10 Products by Revenue')
plt.tight_layout()
plt.show()

# Summary by category
category_summary = supplement_sales.groupby('Category').agg(
    Total_Revenue=('Revenue', 'sum'),
    Average_Price=('Price', 'mean'),
    Total_Units_Sold=('Units Sold', 'sum')
).reset_index()
print(category_summary.sort_values('Total_Revenue', ascending=False))

# Summary by platform
totals_platform = supplement_sales.groupby('Platform').agg(
    Revenue=('Revenue', 'sum'),
    Units_Sold=('Units Sold', 'sum')
).reset_index()
print(totals_platform.sort_values('Revenue', ascending=False))

# Platform comparison plots
plt.figure(figsize=(12, 5))
plt.subplot(1, 2, 1)
sns.barplot(data=totals_platform, x='Platform', y='Revenue', palette='rocket')
plt.title('Revenue by Platform')
plt.subplot(1, 2, 2)
sns.barplot(data=totals_platform, x='Platform', y='Units_Sold', palette='mako')
plt.title('Units Sold by Platform')
plt.tight_layout()
plt.show()



# Load cleaned data
supplement_sales = load_and_process_sales("/Users/vernesapodrimaj/Downloads/Supplement_Sales_Weekly_Expanded.csv")
supplement_sales.head()
