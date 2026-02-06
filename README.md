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

### 📊 Data Sources

This project integrates two primary datasets sourced from **Kaggle**, covering over a century of aviation history to provide a robust foundation for safety analysis:

1. **Aviation Accident Database & Synopses**: Contains detailed data from the National Transportation Safety Board (NTSB) regarding civil aviation accidents and selected incidents within the United States and international waters.
* [View Dataset on Kaggle](https://www.kaggle.com/datasets/khsamaha/aviation-accident-database-synopses)


2. **Airplane Crashes and Fatalities Since 1908**: A comprehensive historical record of commercial, military, and private aircraft crashes worldwide.
* [View Dataset on Kaggle](https://www.kaggle.com/datasets/saurograndi/airplane-crashes-since-1908)



---

### 📂 Project Structure

The repository is organized into modular SSIS packages to ensure a maintainable and scalable ETL pipeline. Each package handles a specific part of the **Star Schema** architecture.

#### **ETL Logic (SSIS Packages)**

* **`DimensionDate.dtsx`**: Automates the creation of the temporal dimension. It extracts date attributes and transforms them into hierarchies (Year, Month, Day) to enable time-series analysis in Power BI.
* **`DimensionFlight.dtsx`**: Processes aircraft-specific data. It handles the cleaning of `Operator` names, `Aircraft Types`, and standardizes `Flight Phases` (e.g., Takeoff, Landing).
* **`DimensionDamages.dtsx`**: Implements the core business logic. This package classifies the severity of an incident as **"Grave"** or **"Leger"** by evaluating casualty counts and the physical state of the aircraft.
* **`TableDeFaits_Accidents.dtsx`**: The main orchestration package. It performs **Lookup Transformations** against the Dimension tables to resolve surrogate keys and loads the final validated data into the central Fact table.

#### **Project Configuration**

* **`Integration Services Project4.dtproj`**: The master Visual Studio project file that manages the metadata and deployment settings for the SSIS solution.
* **`Project.params`**: Contains project-level parameters used to manage environment variables (such as file paths) without hardcoding values.
* **`*.conmgr` file**: Connection manager that define the source (Flat Files/CSV) and destination (SQL Server OLE DB) endpoints for the data flow.

---

## 👤 Author
**[Aymane Ziane]**
