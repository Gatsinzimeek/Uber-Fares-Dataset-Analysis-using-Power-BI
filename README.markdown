# 🌌 Introduction

This project dives into the Uber Fares Dataset to uncover the dynamics of ride fares, trip distances, and temporal patterns. By leveraging Python for data preparation and Power BI for visualization, we create an interactive dashboard that transforms raw data into actionable insights. This analysis empowers stakeholders to understand Uber’s operational landscape and optimize pricing, driver allocation, and market expansion strategies.

### 🎯 Objectives
- **Understand**: Conduct thorough exploratory data analysis (EDA) to grasp dataset structure and quality.  
- **Prepare**: Clean and enhance the dataset for robust analysis.  
- **Analyze**: Uncover relationships between fares, distances, and temporal factors.  
- **Visualize**: Build an interactive Power BI dashboard to showcase trends.  
- **Recommend**: Provide data-driven insights to improve Uber’s operations.

---

## 🛠️ Analytical Workflow

### 1. 📡 Data Understanding and Preparation
- **Download**: Sourced the Uber Fares Dataset from Kaggle, a comprehensive collection of ride data.  
- **Load**: Imported the dataset into a Pandas DataFrame using Python.  
- **Explore**: Performed comprehensive EDA to assess dataset structure, dimensions, data types, and initial quality.  
- **Clean**: Handled missing values, filtered unrealistic fares, and validated coordinates.  
- **Export**: Saved the cleaned dataset as `uber.csv` for Power BI import.

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Display settings
pd.set_option('display.max_columns', None)

# Load the Dataset
df = pd.read_csv("uber.csv")
print("Dataset loaded successfully.")
df.head()

# Explore the Dataset
print("Shape of the dataset:", df.shape)
print("\nData Types and Missing Values:")
print(df.info())
print("\nSummary Statistics:")
print(df.describe())
print("\nMissing Values Count:")
print(df.isnull().sum())

# Clean the Data
# Drop rows with missing values
df_cleaned = df.dropna()

# Filter out unrealistic fare amounts and coordinates
df_cleaned = df_cleaned[
    (df_cleaned['fare_amount'] > 0) &
    (df_cleaned['fare_amount'] < 200) &
    (df_cleaned['pickup_latitude'].between(-90, 90)) &
    (df_cleaned['pickup_longitude'].between(-180, 180))
]

print("Cleaned dataset shape:", df_cleaned.shape)
df_cleaned.head()

# Feature Engineering
# Convert pickup_datetime to datetime type
df_cleaned['pickup_datetime'] = pd.to_datetime(df_cleaned['pickup_datetime'])

# Extract time features
df_cleaned['hour'] = df_cleaned['pickup_datetime'].dt.hour
df_cleaned['day'] = df_cleaned['pickup_datetime'].dt.day
df_cleaned['month'] = df_cleaned['pickup_datetime'].dt.month
df_cleaned['day_of_week'] = df_cleaned['pickup_datetime'].dt.dayofweek

# Add peak/off-peak label
def is_peak(hour):
    return 'Peak' if 7 <= hour <= 9 or 17 <= hour <= 19 else 'Off-Peak'

df_cleaned['peak'] = df_cleaned['hour'].apply(is_peak)

df_cleaned.head()

# Export the Cleaned Dataset
df_cleaned.to_csv("uber.csv", index=False)
print("Cleaned dataset saved as 'uber.csv'")
print(df_cleaned.describe())
```

![Cleaned Dataset](https://github.com/user-attachments/assets/f041c7cf-73be-4243-912a-5fb87de5c264)

**Why this?**

This script loads the Uber dataset, performs EDA to understand its structure and quality, cleans it by removing invalid data, and enriches it with temporal features. The cleaned dataset is saved for Power BI import, ensuring a solid foundation for analysis.

---

### 2. 📊 Exploratory Data Analysis (EDA)
- **Descriptive Statistics**: Generated mean, median, mode, standard deviation, quartiles, and ranges, identifying outliers in fare and distance.  
- **Visualizations**: Created plots to analyze fare distribution and relationships.  
- **Relationships**: Explored correlations between fare amount, distance, and time of day.

#### a. Descriptive Statistics
- **🔍 Insight**: Fares are right-skewed, with most under $20; outliers indicate surge pricing or long trips.  
- **💡 Impact**: Highlights the prevalence of short, affordable rides.

```python
print(df_cleaned.describe())
```

#### b. Fare and Distance Distribution
- **🔍 Insight**: Most rides are short, with fares under $20, but outliers suggest surge pricing or long-distance trips.  
- **💡 Impact**: Guides pricing strategies and anomaly detection.

```python
plt.figure(figsize=(12, 6))

plt.subplot(1, 2, 1)
sns.boxplot(y=df_cleaned['fare_amount'])
plt.title('Boxplot of Fare Amount')

plt.subplot(1, 2, 2)
sns.boxplot(y=df_cleaned['distance'])
plt.title('Boxplot of Distance')

plt.tight_layout()
plt.show()
```
- **Output**:
![Fare and Distance](https://github.com/user-attachments/assets/1086d0d6-96c1-4515-8de6-857810a9eac2)

#### c. Fare Range Analysis ($0–$100)
- **🔍 Insight**: Fares predominantly fall in the $5–$10 range, with frequency decreasing as fares rise.  
- **💡 Impact**: Reinforces Uber’s appeal for affordable rides, informing promotional strategies.

```python
reasonable_fares = df_cleaned[(df_cleaned['fare_amount'] > 0) & (df_cleaned['fare_amount'] <= 100)]

plt.figure(figsize=(10, 6))
sns.histplot(reasonable_fares['fare_amount'], bins=30, kde=True)
plt.title('Distribution of Uber Fare Amounts (0 to $100)')
plt.xlabel('Fare Amount ($)')
plt.ylabel('Frequency')
plt.show()
```
- **Output**:
![Fare Range](https://github.com/user-attachments/assets/39324b1f-a798-4fe6-8a21-f6dac9aa2251)

#### d. Relationships: Fare vs. Distance & Hourly Trends
- **🔍 Scatter Plot Insight**: A linear relationship confirms distance as a primary fare driver.  
- **🔍 Boxplot Insight**: Evening hours show wider fare variability, likely due to surge pricing.  
- **💡 Impact**: Validates pricing models and suggests dynamic driver allocation during peak hours.

```python
plt.figure(figsize=(15, 6))

# Fare amount vs. distance
plt.subplot(1, 2, 1)
plt.scatter(reasonable_fares['distance'], reasonable_fares['fare_amount'], alpha=0.5)
plt.title('Fare Amount vs. Distance')
plt.xlabel('Distance (km)')
plt.ylabel('Fare Amount ($)')

# Fare amount vs. hour of the day
plt.subplot(1, 2, 2)
sns.boxplot(x=reasonable_fares['hour'], y=reasonable_fares['fare_amount'])
plt.title('Fare Amount vs. Hour of the Day')
plt.xlabel('Hour of the Day')
plt.ylabel('Fare Amount ($)')

plt.tight_layout()
plt.show()
```
- **Output**:
![Distance vs Fare](https://github.com/user-attachments/assets/fe5f24b5-2ec6-42ae-9a89-18fd2c0d402d)

---

### 3. 🌟 Feature Engineering
- **New Features**: Extracted hour, day, month, and day of week from timestamps; added peak/off-peak indicators.  
- **Encoding**: Categorized day of week and peak/off-peak as categorical variables.  
- **Export**: Saved enhanced dataset as `uber.csv` for Power BI import.

- **🔍 Insight**: Temporal features reveal peak demand during morning (7–9 AM) and evening (5–7 PM) commutes.  
- **💡 Impact**: Enables targeted analysis of high-demand periods.

---

### 4. 📈 Data Analysis in Power BI
- **Import**: Loaded the enhanced `uber.csv` into Power BI Desktop.  
- **Visualizations**: Created plots to analyze fare patterns, ride distributions, and seasonal trends.  
- **Busiest Periods**: Identified peak demand in morning and evening commutes, with Fridays and Saturdays showing high ride volumes.  
- **Weather Impact**: Not analyzed due to lack of weather data in the dataset.

#### a. Fare vs. Distance Scatter
- **Type**: Scatter Plot  
- **Axis**: `distance` vs. `fare_amount`  
- **Insight**: Confirms linear relationship, with outliers indicating surge pricing or long trips.

![Fare vs Distance](https://github.com/user-attachments/assets/1fbd797e-4d5d-4227-913e-9e702f1e5e8d)

#### b. Average Fare by Hour
- **Type**: Line Chart  
- **Axis**: `hour`  
- **Values**: Average `fare_amount`  
- **Insight**: Highlights fare fluctuations during peak hours.

![Average Fare by Hour](https://github.com/user-attachments/assets/56d69a7b-d9bc-4cb5-b309-bb79b18660a0)

#### c. Fare by Day of Week
- **Type**: Clustered Column Chart  
- **Axis**: `day_of_week`  
- **Values**: `fare_amount`  
- **Insight**: Identifies higher fares on Fridays and Saturdays.

![Fare by Day](https://github.com/user-attachments/assets/e0999d18-4eae-4961-b5f3-dcfb5116a78b)

#### d. Pickup Distribution
- **Type**: Line and Stacked Column Chart  
- **Insight**: Approximates geospatial patterns, with Manhattan dominating ride activity.

![Pickup Distribution](https://github.com/user-attachments/assets/6201ffdc-ce42-48b5-b636-cd324aa8a153)

#### e. Monthly Fare Trends
- **Type**: Area Chart  
- **Axis**: `month`  
- **Values**: `fare_amount`  
- **Insight**: Shows seasonal surges, especially in summer months.

![Monthly Trends](https://github.com/user-attachments/assets/e75b2e1b-b559-4d23-9c9a-a7a07e89dfce)

#### f. Pickup Date Time by Day
- **Type**: Donut Chart  
- **Legend**: `day_of_week`  
- **Values**: `pickup_datetime`  
- **Insight**: Highlights weekly ride distribution.

<img width="259" height="201" alt="donut" src="https://github.com/user-attachments/assets/8b11847d-a44d-4031-9d91-e7f939507472" />

---

### 5. 🖼️ Dashboard Creation in Power BI
- **Design**: Built an interactive dashboard integrating all visualizations.  
- **Features**:  
  - **Slicers**: Filters for hour, day, and month to enable dynamic exploration.  
  - **Interactivity**: Drill-down capabilities for detailed analysis.  
  - **Formatting**: Consistent colors, clear titles, and professional design.  
- **Purpose**: Provides stakeholders with an intuitive tool to explore trends and patterns.

![Dashboard](https://github.com/user-attachments/assets/d3115c7f-2d5c-4313-a941-33b57983d2c4)

---

## 🔑 Key Insights

### 💵 Fare Trends
- Most fares are between **$5–$20**, reflecting short, urban trips.  
- **High-value rides (>$100)** are rare, often linked to airport or suburban travel.  
- Fare distribution is **right-skewed**, with occasional premium trips.

### ⏰ Time Patterns
- Peak demand occurs during **morning (7–9 AM)** and **evening (5–7 PM)** commutes.  
- **Fridays and Saturdays** see the highest ride volumes due to social activities.  
- **Summer months** show increased ride activity, indicating seasonal trends.

### 🗺️ Geographic Trends
- **Manhattan** dominates ride activity, particularly in **Midtown and Downtown**.  
- High-value rides often involve **transportation hubs** like airports.  
- **Outer boroughs** are underserved, presenting growth opportunities.

### 📏 Distance & Fare
- **Fares strongly correlate** with trip distance.  
- **Surge pricing** drives higher fares during peak hours, balancing supply and demand.

---

## ✅ Conclusions & Recommendations
Uber’s NYC data highlights a focus on short, affordable rides, with occasional high-value trips and clear temporal patterns.

### Recommendations:
- 🔍 **Analyze Long Rides**: Investigate high-fare trips to refine pricing models.  
- 📈 **Promote Off-Peak Rides**: Introduce incentives to boost demand during low-traffic hours.  
- 🌍 **Expand to Outer Boroughs**: Target underserved areas with marketing and partnerships.  
- 💹 **Optimize Surge Pricing**: Evaluate its impact on customer satisfaction and driver availability.

---

## 📦 Deliverables
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/11Y-qT8B_T7NQlpNfsatH6-rvdbSYFY7y?usp=sharing)
- 📊 **Power BI Dashboard**: Interactive `.pbix` file  
- 📁 **Dataset**: Enhanced dataset `uber.csv`  
- 📓 **Jupyter Notebook**: Python code for data processing and EDA  
- 📄 **README**: This detailed documentation  
- 🌐 **GitHub Repository**: Public repository  
- 🔗 **Link to uber.csv**: [Click here](https://drive.google.com/file/d/1NRr3bcLfb7ho9KORSVr1RGTAIJsL_Tnf/view?usp=drive_link)

---

## 🛡️ Academic Integrity
This project is an **original work**, crafted through rigorous analysis using Python and Power BI. All insights are authentic and uphold **academic excellence and integrity**.

---

## 🌟 Why This Project Stands Out
This analysis blends **technical rigor** with **engaging storytelling**. From data cleaning to interactive dashboards, each step reflects a commitment to **quality, data-driven insights**, and **visual excellence**.

---

✍️ *Created with Care by **Gatsinzi Ernest***  
*Turning Data into Insights for a Better Future 🚀*

> **Proverbs 16:3**  
> *"Commit to the Lord whatever you do, and he will establish your plans."*