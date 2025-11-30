# Book-Inventory-Web-Scraper

## Executive Summary

Book-Inventory-Web-Scraper is an end-to-end data analytics project designed to optimize inventory management for an online bookstore. The project addresses a critical business gap: the lack of visibility into inventory risks versus revenue potential.

By engineering a full ETL pipeline—scraping 1,000 product records, transforming unstructured web data with Python, and visualizing KPIs in Power BI—this solution provides stakeholders with actionable insights into stock levels, pricing effectiveness, and product quality perception.

## Repository Structure

This repository is organized as follows:

Book-Inventory-Web-Scraper/
├── raw_books_data.csv           # Initial raw dataset extracted via web scraping
├── cleaned_books_data.csv       # Transformed dataset with engineered features
├── clean_data.ipynb             # Jupyter Notebook containing the ETL pipeline
├── Strategic_Inventory_Analysis.pbix # Power BI Dashboard file containing the data model and visuals
└── README.md                    # Project documentation

## Technical Architecture

### 1. Data Acquisition (Web Scraping)
- **Source:** Books to Scrape (Sandbox Environment)
- **Tool:** Instant Data Scraper (Chrome Extension)
- **Scope:** Full crawl of 50 pagination cycles extracting Product Title, Price, Image URL, and Product Page URL.

### 2. ETL & Data Processing (Python)
The data cleaning logic is encapsulated in `clean_data.ipynb` using the Pandas library.

- **Data Cleaning Operations:**
  - Currency Normalization: Removal of non-numeric characters (GBP symbol) and conversion to float precision.
  - Column Mapping: Renaming technical HTML identifiers (e.g., "product_pod") to business-standard naming conventions.

- **Feature Engineering:**
  - **Stock Simulation:** Implementation of a weighted random distribution algorithm to simulate realistic inventory constraints (80% Healthy Stock / 20% Critical Low Stock).
  - **Revenue Calculation:** Derivation of "Potential Revenue" metrics based on Unit Price multiplied by Stock Count.
  - **Logic Generation:** Creation of "Inventory Status" flags for conditional formatting in the dashboard.

### 3. Business Intelligence (Power BI)
- **Data Modeling:** Single-table flat schema optimized for Natural Language Query (Q&A) performance.
- **DAX Implementation:** Custom measures created for aggregation and dynamic KPI tracking.

## Data Dictionary

### Raw Data (raw_books_data.csv)
| Column Name | Data Type | Description |
|---|---|---|
| product_pod | String | The raw title extracted from the HTML container. |
| price_color | String | Price string including currency symbols (e.g., "£51.77"). |
| thumbnail src | String | Relative URL path to the product image. |
| image_container href | String | Relative URL path to the product detail page. |

### Processed Data (cleaned_books_data.csv)
| Column Name | Data Type | Description |
|---|---|---|
| Title | String | Cleaned book title. |
| Price | Decimal | Numeric price value used for calculations. |
| Category | String | Assigned genre (e.g., Fiction, Science). |
| Star_Rating | Integer | Customer rating on a scale of 1-5. |
| Stock_Count | Integer | Current physical inventory count. |
| Inventory_Status | String | Categorical status: "In Stock" vs "Low Stock". |
| Potential_Revenue | Decimal | Calculated field: Price * Stock_Count. |

## Power BI Configuration

### Key DAX Measures
The following DAX formulas were implemented to drive the dashboard KPIs:

**1. Total Revenue Potential**
Calculates the total monetary value of all items currently in stock.
DAX: Total Revenue Potential = SUM(cleaned_books_data[Potential_Revenue])

**2. Low Stock Count**
Dynamic count of unique SKUs that have fallen below the safety stock threshold (5 units).
DAX: Low Stock Count = CALCULATE(COUNTROWS(cleaned_books_data), cleaned_books_data[Inventory_Status] = "Low Stock")

**3. Average Price**
Calculates the mean unit price across the entire inventory.
DAX: Avg Price = AVERAGE(cleaned_books_data[Price])

### Dashboard Features
- **Natural Language Query (Q&A):** Configured with synonyms (e.g., "Money" maps to "Potential_Revenue") to allow non-technical users to query the dataset using plain English.
- **Risk Analysis Grid:** A filtered table visualization specifically highlighting high-value items that are critically low on stock.

## How to Run This Project

1. **Environment Setup:**
   Ensure Python 3.x is installed along with the Pandas and NumPy libraries.

2. **Data Processing:**
   Open `clean_data.ipynb` in Jupyter Notebook or Google Colab. Run all cells to process `raw_books_data.csv` and generate the `cleaned_books_data.csv` file.

3. **Dashboard Launch:**
   Open `Strategic_Inventory_Analysis.pbix` in Power BI Desktop. If the data does not load immediately, navigate to "Transform Data" -> "Data Source Settings" and update the file path to point to your local `cleaned_books_data.csv` file.
