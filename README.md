# ✈️ Aviation Accident Data Warehouse & ETL Pipeline

![Project Architecture](https://maulypdnwtsngvoewhgi.supabase.co/storage/v1/object/public/profile-images/I_got_the_2k_202602062142.jpeg)


## 📖 Project Overview
This project is a comprehensive **Data Engineering** solution designed to analyze historical aviation accident data. The goal was to transform raw, unstructured accident logs into a structured **Data Warehouse (Star Schema)** to enable high-performance reporting on accident trends, fatality rates, and safety risk factors.

The solution processes historical data to answer critical safety questions, such as identifying high-risk flight phases, correlating aircraft damage with fatality rates, and tracking accident trends over decades.

## 🎯 Business Requirements & KPIs
Based on the project scope, the Data Warehouse was built to track the following Key Performance Indicators (KPIs):
* **Accident Severity Analysis:** Distinguishing between "Severe" (Grave) and "Minor" (Leger) accidents based on casualty counts.
* **Fatality vs. Survival Rates:** Comparing total deaths against total passengers aboard.
* **Geospatial Analysis:** Mapping accident locations by Country and specific coordinates (Latitude/Longitude).
* **Phase of Flight Risk:** Identifying which flight phases (e.g., Landing, Takeoff, Cruise) have the highest incident frequency.
* **Operator Performance:** Tracking accident counts by airline/operator.

## 🛠️ Tech Stack
* **ETL Tool:** Microsoft Visual Studio (SSIS - SQL Server Integration Services)
* **Database:** Microsoft SQL Server (SSMS)
* **Visualization:** Power BI
* **Data Sources:** Kaggle (Aviation Accident Synopses, Airplane Crashes Since 1908)

---

## 🏗️ Data Architecture (Star Schema)
The Data Warehouse is designed using a **Star Schema** to optimize query performance for Power BI.

### **Fact Table**
* **`Fact_Accidents`**: The central table containing additive measures and foreign keys.
    * *Measures:* Latitude, Longitude, Accident Counts.
    * *Keys:* Links to Date, Flight, and Damage dimensions.

### **Dimension Tables**
* **`Dim_Damage`**: Handles the severity context.
    * Attributes: `Type_Accident` (Derived: Grave vs Leger), `Nombre_Deces` (Fatalities), `Dommages_Avion` (Destroyed/Substantial).
* **`Dim_Flight`**: Context regarding the aircraft and journey.
    * Attributes: `Operator`, `Type_Avion`, `Aeroport_Depart`, `Phase_vol`.
* **`Dim_Date`**: Standard date dimension for temporal analysis.
    * Attributes: `Year`, `MonthName`, `Day`, `FullDate`.

---

## 🔄 ETL Pipeline (SSIS)
The ETL process extracts raw data, transforms it to ensure data quality, and loads it into the warehouse.

### 1. Extract (Data Flow)
* Ingests data from flat files (CSV) and OLE DB sources.

### 2. Transform (Derived Columns & Lookups)
* **Null Handling:** Standardized missing values. For example, replacing NULL Countries with "Inconnu" (Unknown) using SSIS expressions:
    ```sql
    Country == "" ? "Inconnu" : Country
    ```
* **Data Enrichment:** * **Severity Calculation:** Created a `Type_Accident` column logic where accidents with >0 fatalities are flagged as "Grave".
    * **Date Parsing:** Extracted Year, Month, and Day from timestamps to populate `Dim_Date`.
* **Surrogate Keys:** Used **Lookup Transformations** to map incoming business keys to the Data Warehouse surrogate keys (e.g., matching `Date` to `Date_Key`).

### 3. Load
* Data is sorted and loaded into the destination SQL Server tables (`Fact_Accidents` and Dimensions).

---

## 📊 Analytics Dashboard (Power BI)
The final dashboard provides an interactive view of the data:
* **Global Incident Map:** Visualizes high-density accident zones (North America & Europe prominent).
* **Damage Severity:** Pie chart showing 71.5% of recorded accidents resulted in "Substantial" damage.
* **Flight Phase Analysis:** Bar chart revealing that **Landing** and **Takeoff** are the most critical phases for accidents.
* **Fatality Metrics:** Cards displaying total casualties (116.46K) vs. severe accidents (22.84%).

---

## 🚀 How to Run
1.  **Database Setup:** Run the provided `SQL_Schema_Script.sql` to create the database tables.
2.  **ETL Execution:**
    * Open the solution file (`.sln`) in Visual Studio 2019+ with SSDT installed.
    * Configure the **Connection Managers** to point to your local SQL Server instance.
    * Run the package `Main.dtsx`.
3.  **Visualization:** Open the `Dashboard.pbix` file and refresh the data source to point to your local database.

## 👤 Author
**[Aymane Ziane]**
