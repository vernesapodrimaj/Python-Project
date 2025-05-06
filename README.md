# Python-Project
Supplement Sales analysis with Python  

This project explores and analyzes weekly supplement sales data using Python and Pandas. It's part of a portfolio aimed at showcasing practical data analysis skills, including data cleaning, feature engineering, and exploratory insights generation.

## 📂 Project Structure

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

## 🔍 Key Insights (from example results)

- **Top Categories by Revenue**: Vitamin, Mineral, Performance
- **Top Products by Revenue**: Biotin, Zinc, Pre-Workout
- **Price and Revenue Correlation**: Strong positive correlation between price and revenue
- **Units Returned**: Generally low, but slight correlation with units sold

## 💡 Potential Extensions

- Create visualizations (bar charts, line plots, heatmaps)
- Analyze trends over time
- Study the effect of discounts on revenue and returns
- Build a dashboard using tools like Tableau or Power BI


# Supplement Sales Weekly Analysis with Python 📊
 Exploratory data analysis on supplement sales. Includes data inspection, cleaning, and
initial business insights based on weekly product-level transactions.  

import numpy as np  
import pandas as pd  
import matplotlib.pyplot as plt  
import seaborn as sns  

## Loaded dataset
file_path = "/Users/vernesapodrimaj/Downloads/Supplement_Sales_Weekly_Expanded.csv"
supplement_sales = pd.read_csv(file_path)

## Data Exploration

print("Dataset shape:", supplement_sales.shape)
print("\nFirst 5 rows:")
print(supplement_sales.head())

print("\nColumn names:")
print(supplement_sales.columns.tolist())

print("\nChecking for duplicates...")
print("Duplicates found:", supplement_sales.duplicated().sum())

print("\nChecking for missing values...")
print("Total missing values:", supplement_sales.isnull().sum().sum())

print("\nUnique locations:")
print(supplement_sales['Location'].unique())


## Category Analysis
print("\nTop categories by count:")
category_counts = supplement_sales['Category'].value_counts()
print(category_counts)

print("\nMost common product:")
product_counts = supplement_sales['Product Name'].value_counts()
print(product_counts)


## Time Features
supplement_sales['Date'] = pd.to_datetime(supplement_sales['Date'])
supplement_sales['Year'] = supplement_sales['Date'].dt.year
supplement_sales['DayOfWeek'] = supplement_sales['Date'].dt.day_name()


## Sample Slice 
sample_data = supplement_sales.iloc[20:41]
print("\nSample data slice (rows 20–40):")
print(sample_data)



# Category distribution
plt.figure(figsize=(10, 6))
sns.countplot(data=supplement_sales, y='Category', order=category_counts.index)
plt.title("Product Category Distribution")
plt.xlabel("Count")
plt.tight_layout()
plt.savefig("category_distribution.png")
plt.show()

# Revenue vs. Price correlation
plt.figure(figsize=(8, 6))
sns.scatterplot(data=supplement_sales, x='Price', y='Revenue', hue='Category', alpha=0.7)
plt.title("Price vs. Revenue by Category")
plt.tight_layout()
plt.savefig("price_vs_revenue.png")
plt.show()


## Correlation Matrix
corr = supplement_sales[['Units Sold', 'Revenue', 'Discount', 'Units Returned', 'Price']].corr()
print("\nCorrelation matrix:")
print(corr)

plt.figure(figsize=(8, 6))
sns.heatmap(corr, annot=True, cmap="coolwarm", fmt=".2f")
plt.title("Correlation Matrix")
plt.tight_layout()
plt.savefig("correlation_matrix.png")
plt.show()

## Total number of units returned across all records
total_units_returned = supplement_sales["Units Returned"].sum()
print("Total Units Returned:", total_units_returned)


## Barplot: Average price by product
plt.figure(figsize=(12, 6))
sns.barplot(x='Product Name', y='Price', data=supplement_sales)
plt.title('Average Price per Product')
plt.ylabel('Price ($)')
plt.xlabel('Product Name')
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.show()

## Heatmap: Units Sold by Location and Category
heatmap_data = supplement_sales.pivot_table(
    values='Units Sold',
    index='Location',
    columns='Category',
    aggfunc='sum'
)

## Plot the heatmap
plt.figure(figsize=(12, 6))
sns.heatmap(heatmap_data, cmap="Blues", annot=True, fmt='g', cbar=True)
plt.title('Heatmap of Units Sold by Location and Category')
plt.xlabel('Category')
plt.ylabel('Location')
plt.tight_layout()
plt.show()


## Lineplot: Units Sold by Platform Over the Years

Convert 'Date' column to datetime format
supplement_sales['Date'] = pd.to_datetime(supplement_sales['Date'])

## Extract the year from the 'Date' column
supplement_sales['Year'] = supplement_sales['Date'].dt.year

## Group data by year and platform to get total units sold
year_platform_sales = supplement_sales.groupby(['Year', 'Platform'])['Units Sold'].sum().reset_index()

# Plot the trend of units sold by platform over the years
plt.figure(figsize=(10, 6))
sns.lineplot(data=year_platform_sales, x='Year', y='Units Sold', hue='Platform', marker='o')

plt.title('Units Sold by Platform Over the Years')
plt.xlabel('Year')
plt.ylabel('Units Sold')
plt.xticks(rotation=45)
plt.legend(title='Platform', loc='upper left')
plt.tight_layout()
plt.show()

## Grouping Units Sold by Day of the Week and Product

## Create a new column for the name of the day of the week
supplement_sales['Day of Week Name'] = supplement_sales['Date'].dt.day_name()

## Creating a categorical type to ensure correct weekday order
supplement_sales['Day of Week Name'] = pd.Categorical(
    supplement_sales['Day of Week Name'],
    categories=['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'],
    ordered=True
)

## Grouping by Day of Week and Product Name to get total units sold
day_product_sales = supplement_sales.groupby(['Day of Week Name', 'Product Name'])['Units Sold'].sum().reset_index()

## Sorting results by day of week for consistency
day_product_sales = day_product_sales.sort_values('Day of Week Name')

## Displaying the grouped data
print(day_product_sales.head(15))  # Show the top 15 for preview

# Preview: Dataset with newly added columns

# Confirm 'Day of Week Name' and 'Year' were added correctly
supplement_sales[['Date', 'Product Name', 'Units Sold', 'Day of Week Name', 'Year']].head(10)

# Aggregate by Product Name
totals = supplement_sales.groupby('Product Name').agg({'Units Sold': 'sum', 'Revenue': 'sum'}).reset_index()
totals['Revenue'] = totals['Revenue'].round(2)

# Sort and get top 10
top10 = totals.sort_values('Revenue', ascending=False).head(10)
print(top10)

# Plot top 10 products
plt.figure(figsize=(10, 6))
sns.barplot(data=top10, x='Revenue', y='Product Name', palette='viridis')
plt.title('Top 10 Products by Total Revenue')
plt.xlabel('Revenue ($)')
plt.ylabel('Product Name')
plt.tight_layout()
plt.savefig('top10_products_revenue.png')
plt.show()


# Group by Category
category_summary = supplement_sales.groupby('Category').agg(
    Total_Revenue=('Revenue', 'sum'),
    Average_Price=('Price', 'mean'),
    Total_Units_Sold=('Units Sold', 'sum')
).reset_index()

# Format
category_summary['Total_Revenue'] = category_summary['Total_Revenue'].round(2)
category_summary['Average_Price'] = category_summary['Average_Price'].round(2)

# Sort and show
category_summary = category_summary.sort_values('Total_Revenue', ascending=False)
print(category_summary)


# Group by Platform
totals_platform = supplement_sales.groupby('Platform').agg(
    Revenue=('Revenue', 'sum'),
    Units_Sold=('Units Sold', 'sum')
).reset_index()

# Format
totals_platform['Revenue'] = totals_platform['Revenue'].round(2)
totals_platform = totals_platform.sort_values('Revenue', ascending=False)
print(totals_platform)

# Plot revenue and units sold side-by-side
plt.figure(figsize=(12, 5))

# Revenue
plt.subplot(1, 2, 1)
sns.barplot(data=totals_platform, x='Platform', y='Revenue', palette='rocket')
plt.title('Total Revenue by Platform')
plt.xlabel('Platform')
plt.ylabel('Revenue')

# Units Sold
plt.subplot(1, 2, 2)
sns.barplot(data=totals_platform, x='Platform', y='Units_Sold', palette='mako')
plt.title('Total Units Sold by Platform')
plt.xlabel('Platform')
plt.ylabel('Units Sold')

plt.tight_layout()
plt.savefig('platform_analysis_fixed.png')
plt.show()


# Function for clean loading and prep
def load_and_process_sales(filepath):
    return (
        pd.read_csv(filepath, parse_dates=['Date'])
          .assign(
              Year=lambda df: df['Date'].dt.year,
              DayOfWeek=lambda df: df['Date'].dt.day_name()
          )
    )

# Load cleaned data
supplement_sales = load_and_process_sales("/Users/vernesapodrimaj/Downloads/Supplement_Sales_Weekly_Expanded.csv")
supplement_sales.head()
