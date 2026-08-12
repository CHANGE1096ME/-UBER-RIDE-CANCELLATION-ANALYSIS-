# 🚗 Uber Ride Cancellation Analysis

### Solving the Uber Ride Cancellation Crisis with Python

An end-to-end data analytics case study investigating **why Uber ride requests fail**, when failures occur, and how the problem differs between **City → Airport** and **Airport → City** trips.

The project uses **NumPy, Pandas, Matplotlib, and Seaborn** to clean, transform, analyze, visualize, and interpret **6,745 Uber ride requests** from July to December 2016.

---

## 📌 Project Overview

Uber is facing a major ride fulfillment problem: customers request rides, but many requests either result in a **driver cancellation** or a **"No Cars Available"** response.

The goal of this analysis is to answer:

* When do ride failures happen most often?
* Is the problem worse in the City or at the Airport?
* Are failures caused by driver cancellations or lack of available cars?
* Which time periods have the biggest supply-demand gaps?
* What operational changes could Uber make to reduce failed rides?

The analysis shows that the problem is not caused by a single factor. **City and Airport pickups experience different failure patterns and therefore require different solutions.**

---

## 🎯 Business Objective

The primary objective is to identify the **root causes of Uber ride failures** and translate the findings into practical business recommendations.

### Key questions

1. What percentage of ride requests are successfully completed?
2. Which pickup location has more cancellations?
3. When is ride demand highest?
4. When are cars least available?
5. What is causing City → Airport cancellations?
6. What is causing Airport → City "No Cars Available" requests?
7. How can Uber improve driver availability during critical periods?

---

## 📊 Dataset

The dataset contains:

* **6,745 ride requests**
* Data period: **July–December 2016**
* Trips between a **City and an Airport**
* **6 original columns**

| Column              | Description                                     |
| ------------------- | ----------------------------------------------- |
| `Request id`        | Unique ride request ID                          |
| `Pickup point`      | City or Airport                                 |
| `Driver id`         | Assigned driver                                 |
| `Status`            | Trip Completed, Cancelled, or No Cars Available |
| `Request timestamp` | Time when the ride was requested                |
| `Drop timestamp`    | Time when the completed ride ended              |

## The dataset contains missing `Driver id` and `Drop timestamp` values. Missing drop timestamps are expected for rides that were not completed.

## 🛠️ Tech Stack

### Programming Language

* Python

### Libraries

* **NumPy** — numerical calculations and array operations
* **Pandas** — data loading, cleaning, transformation, filtering, and aggregation
* **Matplotlib** — data visualization
* **Seaborn** — statistical visualization

### Environment

* Google Colab
* Jupyter Notebook

The project imports all four core libraries at the beginning of the analysis.

---

## 🔄 Project Workflow

```text
Business Problem
       ↓
Load Dataset
       ↓
Data Exploration
       ↓
Data Cleaning
       ↓
Feature Engineering
       ↓
Exploratory Data Analysis
       ↓
Visualizations
       ↓
GroupBy & Aggregation
       ↓
Root Cause Analysis
       ↓
Business Recommendations
```

---

## 🗺️ Project Roadmap

The notebook is structured into eight modules:

```text
MODULE 0 → Business Problem
MODULE 1 → Importing Libraries
MODULE 2 → NumPy Basics
MODULE 3 → Pandas: Load & Explore
MODULE 4 → Feature Engineering
MODULE 5 → Matplotlib Charts
MODULE 6 → Seaborn Charts
MODULE 7 → GroupBy & Pivot Tables
MODULE 8 → Root Cause & Recommendations
```

---

# 🔍 Analysis Performed

## 1. Data Loading & Exploration

The Uber dataset was loaded using Pandas:

```python
df = pd.read_csv("uber-data.csv")
```

The dataset contains:

```text
6,745 rows
6 original columns
```

The project also examined:

* Dataset shape
* Data types
* Missing values
* Duplicate records
* Unique status values
* Pickup locations

## There were **0 duplicate rows** in the dataset.

# 🧹 Data Cleaning

The timestamp columns were converted from strings to datetime objects:

```python
df['Request timestamp'] = pd.to_datetime(
    df['Request timestamp']
)

df['Drop timestamp'] = pd.to_datetime(
    df['Drop timestamp']
)
```

This enabled time-based analysis such as:

* Hour of request
* Time-slot analysis
* Ride duration calculation

## The project also investigated missing values rather than blindly removing them, because a missing `Drop timestamp` can indicate that a ride was not completed.

# ⚙️ Feature Engineering

New analytical features were created from the original data.

### 1. RequestHour

Extracted the hour from the request timestamp.

```python
df['RequestHour'] = df['Request timestamp'].dt.hour
```

This allowed the analysis to identify hourly demand and failure patterns.

### 2. TimeSlot

The request hour was converted into meaningful time categories:

| Hour  | Time Slot     |
| ----- | ------------- |
| 0–4   | Dawn          |
| 5–9   | Early Morning |
| 10–16 | Noon          |
| 17–21 | Late Evening  |
| 22–23 | Night         |

### 3. Cab Availability

A new binary-style category was created:

```text
Trip Completed      → Available
Cancelled           → Not Available
No Cars Available   → Not Available
```

### 4. Duration_mins

For completed rides, ride duration was calculated using request and drop timestamps.

These features were created specifically to investigate **time-based demand and supply patterns**.

---

# 📈 Exploratory Data Analysis

The project uses several visualization techniques to investigate the ride cancellation problem.

## Visualization 1 — Ride Status Distribution

A bar chart was used to compare:

* Trip Completed
* Cancelled
* No Cars Available

The analysis identified a major fulfillment problem, with approximately **58% of ride requests failing**.

---

## Visualization 2 — City vs Airport Requests

A pie chart was used to compare the overall distribution of requests between:

* City
* Airport

The dataset contains:

```text
City     → 3,507 requests
Airport  → 3,238 requests
```

The number of requests is relatively balanced between the two pickup locations.

---

## Visualization 3 — Hourly Demand

A line chart was used to identify demand patterns throughout the day.

The analysis identified two major demand windows:

```text
🌅 5 AM – 9 AM
🌆 5 PM – 9 PM
```

These periods correspond to the major supply-demand gaps identified later in the analysis.

---

# 🚨 Critical Finding: Two Different Problems

One of the most important findings is that **City and Airport requests fail for different reasons**.

A Seaborn countplot comparing pickup point and ride status revealed:

### 🏙️ City

The dominant failure type is:

**Driver Cancellation**

### 🛬 Airport

The dominant failure type is:

**No Cars Available**

This means Uber should not use a single solution for both locations.

---

# 🏙️ City → Airport Problem

The biggest City → Airport problem occurs during:

### ⏰ 5 AM – 9 AM

During this period, driver cancellations increase significantly.

The analysis suggests that drivers may be discouraged by the economics of airport trips because of:

* Long pickup distances
* Longer airport trips
* Waiting time
* Lack of guaranteed return trips

The City failure analysis showed:

```text
Cancellation Rate: 30.4%
No Cars Available: 26.7%
Completion Rate:    42.9%
```

---

# 🛬 Airport → City Problem

The Airport experiences a different issue.

The biggest problem occurs during:

### ⏰ 5 PM – 9 PM

The dominant failure status is:

**No Cars Available**

The analysis suggests that drivers drop passengers at the airport during the morning but do not necessarily remain there for evening demand.

This creates a structural supply gap.

Airport results:

```text
Cancellation Rate:  6.1%
No Cars Available: 52.9%
Completion Rate:    41.0%
```

---

# 🔥 Cancellation Heatmap

A heatmap was created using:

```text
Request Hour × Pickup Point
```

Only cancelled rides were analyzed.

The heatmap highlighted the **5 AM–9 AM City window** as the major cancellation period.

This reinforces the finding that the morning City → Airport route is strongly associated with driver cancellations.

---

# 🕐 Time-Slot Analysis

The project also analyzed cab availability across:

* Dawn
* Early Morning
* Noon
* Late Evening
* Night

The **Early Morning** and **Late Evening** periods showed the largest supply-demand gaps.

These periods therefore become the most important windows for targeted operational interventions.

---

# 📊 Key Statistics

## Overall

```text
Total Requests       → 6,745
Cancelled            → 1,264
Trip Completed       → 2,831
```

The remaining requests were classified as **No Cars Available**, giving a total of approximately **3,914 failed requests**.

The notebook identifies this as approximately **58% of all ride requests failing**.

---

## By Pickup Location

| Metric            |  City | Airport |
| ----------------- | ----: | ------: |
| Total Requests    | 3,507 |   3,238 |
| Cancellation Rate | 30.4% |    6.1% |
| No Cars Available | 26.7% |   52.9% |
| Completion Rate   | 42.9% |   41.0% |

This table clearly shows the different nature of the two problems.

**City:** Driver cancellation problem

**Airport:** Vehicle availability problem

---

# 🧠 Root Cause Analysis

The analysis identifies three major operational problems.

### 1. Structural Supply-Demand Mismatch

Approximately 58% of requests fail, indicating that the issue is not an isolated event but a broader supply-demand problem.

### 2. Airport Evening Supply Gap

Airport → City demand increases during the evening, but vehicle availability is insufficient.

### 3. Driver Economics for Airport Trips

During the morning City → Airport rush, drivers are more likely to cancel because airport trips may involve:

```text
Long pickup
     +
Long trip
     +
Waiting time
     +
Uncertain return
     =
Low driver incentive
```

The project therefore concludes that **different failure types require different operational solutions**.

---

# 💡 Business Recommendations

## 1. 🛬 Airport Waiting Bonus

Provide drivers with a waiting incentive after dropping passengers at the airport.

**Expected impact:**

Keep more drivers near the airport and reduce evening **No Cars Available** requests.

---

## 2. 🌙 Night Shift Premium

Introduce higher fares for selected low-supply periods.

**Example proposed in the analysis:**

```text
1.5× fare
10 PM – 6 AM
```

This could improve driver participation during supply-constrained periods.

---

## 3. 🚗 Pre-position Fleet

Move a portion of the city fleet toward the airport before expected evening demand.

The analysis proposes:

```text
20% of city fleet
→ Airport
→ Before 4:30 PM
```

This would proactively address the Airport → City evening supply gap.

---

## 4. 🔄 Guaranteed Return Fare

Create a combined:

```text
City → Airport → City
```

booking or guaranteed return incentive.

The goal is to reduce the economic disadvantage drivers may perceive from one-way airport trips.

---

## 5. 📍 Wider Airport Search Radius

Increase the driver search radius around the airport.

The project proposes expanding the search radius from:

```text
3 km → 10 km
```

This could increase the number of eligible drivers and reduce **No Cars Available** responses.

The recommendations and expected impacts are summarized in the notebook's final business recommendation section.

---

# 📌 Final Business Takeaway

The Uber ride cancellation crisis is **not one problem — it is two problems happening at different locations and times.**

### 🏙️ City → Airport

```text
5–9 AM
     ↓
High demand
     ↓
Driver cancellations
     ↓
Low completion
```

### 🛬 Airport → City

```text
5–9 PM
     ↓
High demand
     ↓
Insufficient cars
     ↓
"No Cars Available"
```

Therefore:

> **Uber should use driver incentives for City → Airport morning trips and supply positioning/incentives for Airport → City evening trips.**

This location-and-time-specific approach is more effective than applying one generic solution across all rides.

---

# 📚 Skills Demonstrated

This project demonstrates practical knowledge of:

* Python
* NumPy
* Pandas
* Data Cleaning
* Data Transformation
* Feature Engineering
* Exploratory Data Analysis
* GroupBy
* Pivot Tables
* Time-Series Analysis
* Matplotlib
* Seaborn
* Data Visualization
* Root Cause Analysis
* Business Analytics
* Business Recommendations

---

# 📁 Project Structure

```text
Uber-Ride-Cancellation-Analysis/
│
├── README.md
├── uber-data.csv
├── UBER_RIDE_CANCELLATION_ANALYSIS.ipynb
│
└── images/
    ├── ride_status_distribution.png
    ├── city_airport_split.png
    ├── hourly_demand.png
    ├── ride_outcomes.png
    ├── airport_supply_demand.png
    ├── city_supply_demand.png
    ├── cancellation_heatmap.png
    └── cab_availability_timeslot.png
```

---

# ▶️ How to Run the Project

## Option 1 — Google Colab

1. Open the notebook in Google Colab.
2. Upload `uber-data.csv`.
3. Run the notebook cells sequentially.
4. Explore the generated visualizations and findings.

## Option 2 — Local Jupyter Notebook

Clone the repository:

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
```

Navigate to the project:

```bash
cd Uber-Ride-Cancellation-Analysis
```

Install the required libraries:

```bash
pip install numpy pandas matplotlib seaborn jupyter
```

Start Jupyter:

```bash
jupyter notebook
```

Open:

```text
UBER_RIDE_CANCELLATION_ANALYSIS.ipynb
```

---

# 📦 Requirements

```text
numpy
pandas
matplotlib
seaborn
jupyter
```

---

# 🚀 Future Improvements

The current project focuses primarily on descriptive and exploratory analytics.

Future versions could include:

* Driver-level cancellation analysis
* Day-of-week analysis
* Date-level trend analysis
* Driver availability forecasting
* Demand forecasting
* Predictive modeling for ride cancellation
* Driver incentive optimization
* Interactive Power BI/Tableau dashboard
* Statistical hypothesis testing
* Machine learning model to predict failed rides
* Cost-benefit analysis of proposed incentives

---

# ⚠️ Data Limitation

The dataset contains request, pickup, driver, status, and timestamp information, but does not provide detailed information such as:

* Driver location
* Fare amount
* Distance
* Driver earnings
* Waiting time
* Traffic conditions
* Weather
* Individual driver behavior

Therefore, some root-cause explanations—particularly around **driver economics and airport return behavior**—should be treated as analytical hypotheses supported by observed patterns rather than directly measured causal effects.

---

# 👨‍💻 Author

**Yawan Kumar**

B.Tech — Computer Science & Engineering

### Interests

* Data Analytics
* Business Intelligence
* Python
* SQL
* Data Visualization
* AI & Data Science

---

## ⭐ If you found this project useful

Consider giving the repository a ⭐ on GitHub.

---

## 🔗 Project Links

**Google Colab Notebook:**
https://colab.research.google.com/drive/11wTmG34h4eZGDHDw2c8qJ06aXFQ_1uzB?usp=sharing

**GitHub Repository:**
*Add your GitHub repository link here*

---

### 📌 Project Summary

**Uber Ride Cancellation Analysis | Python | Pandas | NumPy | Matplotlib | Seaborn**

Analyzed **6,745 Uber ride requests** to identify time- and location-based supply-demand gaps. Found that approximately **58% of requests failed**, with **City → Airport morning trips dominated by driver cancellations** and **Airport → City evening trips dominated by vehicle unavailability**. Used feature engineering, GroupBy analysis, pivot tables, and multiple visualizations to identify root causes and propose targeted driver incentives and fleet-positioning strategies.
