# UrbanNest Retail: Data Cleaning and PostgreSQL Loading

## Overview

This project prepares a retail customer and order dataset for storage in PostgreSQL using Python.

The source dataset contains 5,000 rows and 25 columns covering customers, products, orders, payments, delivery, and customer experience.

## Tools

- Python
- pandas
- psycopg2
- PostgreSQL
- Jupyter Notebook
- Git and GitHub

## Workflow

1. Read the source CSV into a pandas dataframe.
2. Create a working copy of the dataset.
3. Inspect column types and missing values.
4. Fill selected missing values.
5. Convert order dates to datetime and phone numbers to strings.
6. Split the dataset into six dataframes.
7. Export the dataframes to CSV.
8. Create PostgreSQL tables and load the exported records.

## Data Preparation

Missing values in the following columns were filled with `"Unknown"`:

- gender
- age_group
- customer_segment
- payment_method
- sales_channel
- return_flag

Missing customer ratings were filled with `0.0`.

Other missing values, including delivery durations, are preserved. During CSV loading, empty fields are converted to Python `None` so PostgreSQL stores them as SQL `NULL`.

The `order_date` column is converted using `pd.to_datetime()`. Phone numbers are treated as text identifiers.

## Exported Files

| File | Contents |
|---|---|
| `customers.csv` | Customer identifiers and demographic attributes |
| `products.csv` | Product identifiers, categories, prices, and discounts |
| `orders.csv` | Orders linked to customers and products |
| `payment_channels.csv` | Payment methods and sales channels |
| `delivery.csv` | Delivery status, duration, and fees |
| `experiences.csv` | Customer ratings and return flags |

These files are exported to the `cleaned_data/` directory.

## PostgreSQL Setup

Create a database named `urban_retail` before running the Python table-creation function:

```sql
CREATE DATABASE urban_retail;
```

The project creates an `urban_retail` schema inside that database.

Configure `get_db_connection()` with the connection details for your local PostgreSQL installation. Keep passwords out of publicly shared code.

### Important Column Types

- Phone numbers: `TEXT`
- Product categories: `TEXT`
- Order dates: `TIMESTAMP WITHOUT TIME ZONE`
- Return flags: `TEXT`
- Ratings and delivery durations: numeric columns

Foreign keys link orders to customers and products, and delivery and experience records to orders.

## Running the Project

Install the required Python packages:

```bash
pip install pandas psycopg2-binary
```

Then run the notebook cells in this order:

1. Import the libraries and read the source dataset.
2. Run the data-preparation steps.
3. Create the six dataframes.
4. Export the cleaned CSV files.
5. Configure the database connection.
6. Run `create_tables()`.
7. Load customers and products.
8. Load orders.
9. Load payment channels, delivery, and experience records.

Relative CSV paths are resolved from the notebook's working directory.

**Warning:** The table-creation function drops existing project tables before recreating them. Running it again deletes their existing data.

## Issues Addressed

- Corrected PostgreSQL syntax and foreign-key references.
- Used PostgreSQL `TIMESTAMP` for date-and-time values.
- Changed phone-number storage from integer to text.
- Converted empty CSV fields to SQL `NULL`.
- Changed return-flag storage from numeric to text.

## Limitations

- Full-row customer deduplication does not guarantee unique customer IDs.
- Orders must have unique order IDs if `order_id` is their primary key.
- Deduplication by an identifier keeps the first record and may discard conflicting attributes.
- Filling missing ratings with zero affects rating averages.
- Phone numbers originally read as integers may have already lost leading zeros.
- Repeated loading can cause duplicate-primary-key errors.
- Table names in the creation and loading queries must match exactly.

## Purpose

This project demonstrates a Python-to-PostgreSQL workflow for preparing retail data, separating related records, and handling common database-loading errors.
