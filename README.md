# E-commerce-Full-Project-Data-Analysis-with-Python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Step 1: Load Dataset
data = pd.read_csv('/content/sales_data_sample.csv', encoding='latin1')

# Step 2: Show all column names and automatically select one
print("All column names in dataset:", data.columns.tolist())
auto_column = data.columns[0]  # Automatically select first column
print(f"Automatically selected column for demo: {auto_column}")

# Step 3: Data Cleaning
# Remove rows with non-positive Quantity or UnitPrice (if these columns exist)
if 'Quantity' in data.columns and 'UnitPrice' in data.columns:
    data = data[(data['Quantity'] > 0) & (data['UnitPrice'] > 0)]
else:
    print("Quantity and/or UnitPrice columns not found. Skipping value filter.")

# Step 4: Feature Engineering
if 'Quantity' in data.columns and 'UnitPrice' in data.columns:
    data['TotalPrice'] = data['Quantity'] * data['UnitPrice']
else:
    data['TotalPrice'] = 0  # fallback if columns not found

if 'InvoiceDate' in data.columns:
    data['InvoiceDate'] = pd.to_datetime(data['InvoiceDate'], errors='coerce')
    data['Year'] = data['InvoiceDate'].dt.year
    data['Month'] = data['InvoiceDate'].dt.month
else:
    print("InvoiceDate column not found. Skipping date features.")

# Step 5: Exploratory Data Analysis (EDA)

# 5.1 Sales Over Time
if 'InvoiceDate' in data.columns:
    sales_over_time = data.groupby('InvoiceDate')['TotalPrice'].sum()
    plt.figure(figsize=(14,6))
    sales_over_time.plot()
    plt.title('Sales Over Time')
    plt.xlabel('Date')
    plt.ylabel('Total Sales')
    plt.tight_layout()
    plt.show()
else:
    print("Cannot plot sales over time. InvoiceDate column missing.")

# 5.2 Top 10 Products by Sales
if 'Description' in data.columns:
    top_products = data.groupby('Description')['TotalPrice'].sum().sort_values(ascending=False).head(10)
    plt.figure(figsize=(10,6))
    sns.barplot(x=top_products.values, y=top_products.index, palette='viridis')
    plt.title('Top 10 Products by Sales')
    plt.xlabel('Total Sales')
    plt.ylabel('Product Description')
    plt.tight_layout()
    plt.show()
else:
    print("Description column not found. Skipping top products plot.")

# 5.3 Top 10 Countries by Sales
if 'Country' in data.columns:
    country_sales = data.groupby('Country')['TotalPrice'].sum().sort_values(ascending=False).head(10)
    plt.figure(figsize=(10,6))
    sns.barplot(x=country_sales.values, y=country_sales.index, palette='magma')
    plt.title('Top 10 Countries by Sales')
    plt.xlabel('Total Sales')
    plt.ylabel('Country')
    plt.tight_layout()
    plt.show()
else:
    print("Country column not found. Skipping country sales plot.")

# Step 6: Save cleaned data (optional)
data.to_csv('cleaned_sales_data.csv', index=False)
print("Data cleaning and analysis complete. File saved.")

# Step 7: Demo - Use the automatically selected column for a simple plot (if it's numeric)
if pd.api.types.is_numeric_dtype(data[auto_column]):
    plt.figure(figsize=(8,5))
    sns.histplot(data[auto_column], bins=20, kde=True)
    plt.title(f'Distribution of {auto_column}')
    plt.xlabel(auto_column)
    plt.ylabel('Frequency')
    plt.tight_layout()
    plt.show()
else:
    print(f"Automatically selected column '{auto_column}' is not numeric. Skipping demo plot.")
