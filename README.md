# CodeAlpha_Unemployment-Analysis-with-python
# 📌 Unemployment Analysis in India
# Importing Libraries
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# ✅ 1. Load the Dataset
file_path = "/mnt/data/Unemployment in India.csv"  # path to uploaded file
data = pd.read_csv(file_path)
print("✅ Dataset Loaded Successfully\n")

# ✅ 2. View Basic Information
print("📌 First 5 rows:\n", data.head())
print("\n📌 Dataset Info:")
print(data.info())
print("\n📌 Missing Values:\n", data.isnull().sum())

# ✅ 3. Data Cleaning
# Standardize column names (remove spaces, lower case)
data.columns = data.columns.str.strip().str.lower().str.replace(' ', '_')
print("\n📌 Cleaned Column Names:", data.columns.tolist())

# Convert Date column to datetime
if 'date' in data.columns:
    data['date'] = pd.to_datetime(data['date'], errors='coerce')

# Drop rows with missing unemployment rate values
data = data.dropna(subset=['unemployment_rate'])

# ✅ 4. Basic Statistics
print("\n📊 Summary Statistics:")
print(data.describe())

# ✅ 5. Trend Visualization Over Time
plt.figure(figsize=(12, 6))
sns.lineplot(x='date', y='unemployment_rate', data=data)
plt.title('📈 Unemployment Rate Over Time')
plt.xlabel('Date')
plt.ylabel('Unemployment Rate (%)')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

# ✅ 6. Regional Analysis (if region/state column exists)
region_col = None
for col in data.columns:
    if 'region' in col or 'state' in col:
        region_col = col
        break

if region_col:
    plt.figure(figsize=(14, 7))
    sns.barplot(x=region_col, y='unemployment_rate', data=data.groupby(region_col)['unemployment_rate'].mean().reset_index())
    plt.title('🌍 Average Unemployment Rate by Region/State')
    plt.xticks(rotation=90)
    plt.tight_layout()
    plt.show()

# ✅ 7. Covid-19 Impact (Optional)
# Assuming Covid started around March 2020
if 'date' in data.columns:
    covid_start = pd.to_datetime('2020-03-01')
    pre_covid = data[data['date'] < covid_start]
    post_covid = data[data['date'] >= covid_start]

    print("\n📌 Avg Unemployment Rate Before Covid:", pre_covid['unemployment_rate'].mean())
    print("📌 Avg Unemployment Rate After Covid:", post_covid['unemployment_rate'].mean())

    plt.figure(figsize=(8, 5))
    sns.barplot(x=['Pre-Covid', 'Post-Covid'], y=[pre_covid['unemployment_rate'].mean(), post_covid['unemployment_rate'].mean()])
    plt.title('🦠 Covid-19 Impact on Unemployment Rate')
    plt.ylabel('Average Unemployment Rate (%)')
    plt.show()

# ✅ 8. Seasonal Trend Analysis (Monthly)
if 'date' in data.columns:
    data['month'] = data['date'].dt.month
    monthly_trend = data.groupby('month')['unemployment_rate'].mean()

    plt.figure(figsize=(10, 5))
    monthly_trend.plot(marker='o')
    plt.title('📆 Average Unemployment Rate by Month (Seasonal Trend)')
    plt.xlabel('Month')
    plt.ylabel('Unemployment Rate (%)')
    plt.grid(True)
    plt.show()

print("\n✅ Unemployment Analysis Completed Successfully")
