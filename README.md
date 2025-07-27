
# 🌌 Unveiling Uber's Fare Dynamics



## 🌠 Project Vision

Embark on an analytical voyage to decode the pulse of Uber’s fare ecosystem! This project harnesses the Uber Fares Dataset to illuminate the intricate interplay of ride fares, trip distances, and temporal patterns. Through Python’s analytical precision and Power BI’s visual artistry, we craft an immersive dashboard that transforms raw data into a symphony of insights, empowering stakeholders to navigate Uber’s operational landscape with clarity and confidence.

### 🎯 Objectives
- **Discover**: Dive deep into Exploratory Data Analysis (EDA) to uncover dataset intricacies.  
- **Refine**: Purify the dataset by addressing inconsistencies and outliers.  
- **Innovate**: Engineer features to amplify analytical depth.  
- **Visualize**: Create a captivating Power BI dashboard to showcase trends across time and space.  
- **Empower**: Deliver actionable insights to optimize Uber’s operations and strategy.

---

## 🛠️ Analytical Architecture

### 1. 📡 Data Acquisition & Exploration
- **Source**: Extracted the Uber Fares Dataset from Kaggle, a treasure trove of ride data.  
- **Tool**: Leveraged Jupyter Notebook with Pandas for initial exploration.  
- **Actions**: Probed dataset structure, data types, and anomalies to lay a solid analytical foundation.

![Dataset Preview](https://github.com/user-attachments/assets/fddab80d-f80e-4330-98fe-09b5b410bbd1)

---

### 2. 🧬 Data Purification
- **Cleansing**: Removed null values and outliers in fare and distance fields to ensure data integrity.  
- **Transformation**: Standardized timestamps into datetime formats for seamless temporal analysis.

```python
from google.colab import drive
drive.mount('/content/drive')
import pandas as pd
import numpy as np

# Load dataset
uber_df = pd.read_csv('uber_fares.csv')

# Drop missing values
uber_df_clean = uber_df.dropna()

# Filter out invalid fares
uber_df_clean = uber_df_clean[uber_df_clean['fare_amount'] > 0]

# Restrict passenger counts to realistic range (1-6)
uber_df_clean = uber_df_clean[(uber_df_clean['passenger_count'] >= 1) & (uber_df_clean['passenger_count'] <= 6)]

# Define NYC geographic bounds
lon_min, lon_max = -74.05, -73.75
lat_min, lat_max = 40.63, 40.85

# Filter valid pickup and dropoff coordinates
uber_df_clean = uber_df_clean[
    (uber_df_clean['pickup_longitude'].between(lon_min, lon_max)) &
    (uber_df_clean['pickup_latitude'].between(lat_min, lat_max)) &
    (uber_df_clean['dropoff_longitude'].between(lon_min, lon_max)) &
    (uber_df_clean['dropoff_latitude'].between(lat_min, lat_max))
]

# Convert pickup_datetime to datetime
uber_df_clean['pickup_datetime'] = pd.to_datetime(uber_df_clean['pickup_datetime'], errors='coerce')
uber_df_clean = uber_df_clean.dropna(subset=['pickup_datetime'])

# Drop irrelevant columns
uber_df_clean = uber_df_clean.drop(columns=['Unnamed: 0', 'key'], errors='ignore')

# Output cleaned dataset
print("Cleaned dataset shape:", uber_df_clean.shape)
uber_df_clean.head()
```

![Cleaned Dataset](https://github.com/user-attachments/assets/f041c7cf-73be-4243-912a-5fb87de5c264)

**Why this?**

This cleans and prepares a raw Uber dataset for analysis. It filters out invalid and irrelevant data, such as trips with incorrect fares or coordinates outside of New York City. It then enriches the data by creating new features like the time of day and day of the week. Finally, it visualizes the cleaned data to reveal patterns and relationships, and saves the enhanced dataset for further use.

### Now you can save the file as uber_fares_enhanced.csv.
```python
# Save the enhanced dataset
uber_df_encoded.to_csv('uber_fares_enhanced.csv', index=False)

print("Successfully saved the enhanced dataset to 'uber_fares_enhanced.csv'")
```
---


### 3. 🌟 Feature Engineering
The analysis reveals a strong correlation between fare amounts and trip distances, with most rides concentrated in Manhattan and fares typically under $20. Evening hours exhibit greater fare variability, hinting at surge pricing dynamics.

- **Output**: Exported enriched dataset as `uber_fares_enhanced.csv`.

#### a. Fare and Distance Distribution
- **🔍 Insight**: Most rides are short, with fares under $20, but outliers suggest surge pricing or long-distance trips.  
- **💡 Impact**: Highlights Uber’s focus on short trips, guiding pricing strategies and anomaly detection.

```python
import seaborn as sns
import matplotlib.pyplot as plt

plt.figure(figsize=(12, 6))

plt.subplot(1, 2, 1)
sns.boxplot(y=uber_df_cleaned['fare_amount'])
plt.title('Boxplot of Fare Amount')

plt.subplot(1, 2, 2)
sns.boxplot(y=uber_df_cleaned['distance'])
plt.title('Boxplot of Distance')

plt.tight_layout()
plt.show()
```
- **Output**:
![Fare and Distance](https://github.com/user-attachments/assets/1086d0d6-96c1-4515-8de6-857810a9eac2)

#### b. Fare Range Analysis ($0–$100)
- **🔍 Insight**: Fares predominantly fall in the $5–$10 range, with frequency decreasing as fares rise.  
- **💡 Impact**: Reinforces Uber’s appeal for affordable rides, informing promotional strategies.
```python
# Filter out extreme outliers for a more informative visualization
reasonable_fares = uber_df_cleaned[(uber_df_cleaned['fare_amount'] > 0) & (uber_df_cleaned['fare_amount'] <= 100)]

plt.figure(figsize=(10, 6))
sns.histplot(reasonable_fares['fare_amount'], bins=30, kde=True)
plt.title('Distribution of Uber Fare Amounts (0 to $100)')
plt.xlabel('Fare Amount ($)')
plt.ylabel('Frequency')
plt.show()
```
- **Output**:
![Fare Range](https://github.com/user-attachments/assets/39324b1f-a798-4fe6-8a21-f6dac9aa2251)

#### c. Distance vs. Fare & Hourly Fare Trends
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

### 4. 🎨 Power BI Visualizations
A constellation of visuals transforms data into compelling narratives, offering intuitive insights into Uber’s operational dynamics.

#### 📈 Visual 1: Fare vs. Distance Scatter
- **Type**: Scatter Plot  
- **Axis**: `distance` vs. `fare_amount`  
- **Insight**: Visualizes the linear relationship between trip length and fare, flagging outliers for review.

![Fare vs Distance](https://github.com/user-attachments/assets/1fbd797e-4d5d-4227-913e-9e702f1e5e8d)

#### 📊 Visual 2: Average Fare by Hour
- **Type**: Line Chart  
- **Axis**: `pickup_hour`  
- **Values**: Average `fare_amount`  
- **Insight**: Reveals fare fluctuations, guiding dynamic pricing strategies.

![Average Fare by Hour](https://github.com/user-attachments/assets/56d69a7b-d9bc-4cb5-b309-bb79b18660a0)

#### 📅 Visual 3: Fare by Day of Week
- **Type**: Clustered Column Chart  
- **Axis**: `pickup_dayofweek`  
- **Values**: `fare_amount`  
- **Insight**: Identifies weekly trends for operational planning.

![Fare by Day](https://github.com/user-attachments/assets/e0999d18-4eae-4961-b5f3-dcfb5116a78b)

#### 🗺️ Visual 4: Pickup Distribution
- **Type**: Line and Stacked Column Chart (map alternative)  
- **Insight**: Approximates geospatial ride patterns for market analysis.

![Pickup Distribution](https://github.com/user-attachments/assets/6201ffdc-ce42-48b5-b636-cd324aa8a153)

#### 📆 Visual 5: Monthly Fare Trends
- **Type**: Area Chart  
- **Axis**: `month`  
- **Values**: `fare_amount`  
- **Insight**: Highlights seasonal trends for strategic planning.

![Monthly Trends](https://github.com/user-attachments/assets/e75b2e1b-b559-4d23-9c9a-a7a07e89dfce)

---
#### 🍩 Visual 6: Pick up Date Time by Day
- **Type**: Donut Chart  
- **Legend**: `Day`  
- **Values**: `pickup_datetime`  
- **Insight**: Highlights seasonal trends for strategic planning.
<img width="259" height="201" alt="donut" src="https://github.com/user-attachments/assets/8b11847d-a44d-4031-9d91-e7f939507472" />


### 5. 🖼️ Interactive Dashboard
- **Design**: A unified Power BI dashboard weaving all visuals into a seamless experience.  
- **Features**:  
  - **Slicers**: Enable filtering by hour, day, and month for dynamic exploration.  
  - **Aesthetics**: Vibrant colors, clear titles, and intuitive tooltips.  
- **Purpose**: Empowers stakeholders to interact with data and uncover insights effortlessly.

![Dashboard](https://github.com/user-attachments/assets/d3115c7f-2d5c-4313-a941-33b57983d2c4)

---

## 🔑 Key Insights

### 💵 Fare Trends
- Most fares are between **$5–$20**, reflecting short, urban trips.  
- **High-value rides (>$100)** are rare and usually linked to airport or suburban travel.  
- The fare distribution is **right-skewed**, with occasional premium trips.

### ⏰ Time Patterns
- Peak demand occurs during **morning (7–9 AM)** and **evening (5–7 PM)** commutes.  
- **Fridays and Saturdays** see the highest ride volumes due to social and leisure activities.  
- **Summer months** experience a surge in ride activity, showing seasonal influence.

### 🗺️ Geographic Trends
- **Manhattan** dominates ride activity, especially in **Midtown and Downtown**.  
- High-value rides often start or end at **transportation hubs** like airports.  
- **Outer boroughs** are underserved, offering potential for growth.

### 📏 Distance & Fare
- **Fares strongly correlate** with trip distance.  
- **Surge pricing** increases fares during high-demand hours, helping balance supply and demand.

---

## ✅ Conclusions & Recommendations
Uber’s NYC data reflects the city's fast-paced lifestyle, where short, affordable rides are common, and longer trips are occasional but significant.

### Recommendations:
- 🔍 **Analyze Long Rides**: Study high-fare trips to optimize pricing strategies.  
- 📈 **Boost Off-Peak Rides**: Offer promotions to stimulate demand during quiet hours.  
- 🌍 **Expand Reach**: Target outer boroughs with incentives and partnerships.  
- 💹 **Evaluate Surge Pricing**: Assess its impact on customer satisfaction and ride availability.

---

## 📦 Deliverables
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/11Y-qT8B_T7NQlpNfsatH6-rvdbSYFY7y?usp=sharing)
- 📊 **Power BI Dashboard**: Interactive `.pbix` file  
- 📁 **Dataset**: Cleaned dataset `uber_fares_enhanced.csv`  
- 📓 **Jupyter Notebook**: Python code for data processing and visualization  
- 📄 **README**: This detailed documentation  
- 🌐 **GitHub Repository**: Public repository.
- 🔗 **Link to uber_fares_enhanced.csv**: [Click here](https://drive.google.com/file/d/1NRr3bcLfb7ho9KORSVr1RGTAIJsL_Tnf/view?usp=drive_link)


---

## 🛡️ Academic Integrity
This project is an **original work**, created through rigorous analysis using Python and Power BI. All insights are authentic and uphold the values of **academic excellence and integrity**.

---

## 🌟 Why This Project Stands Out
This analysis combines **technical depth** with **clear storytelling**. From cleaning and feature engineering to interactive dashboards, each step reflects a passion for **quality, data-driven thinking**, and **visual design**.

---

✍️ *Created with Care by **MUGENI Cynthia***  
*Turning Data into Insights for a Better Future 🚀*

> **Proverbs 12:24**  
> *"The hand of the diligent will rule, while the slothful will be put to forced labor."*
