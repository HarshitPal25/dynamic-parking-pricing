# 🅿️ Dynamic Pricing for Urban Parking Lots

This project demonstrates a **real-time dynamic pricing system** for urban parking lots.  
It uses **Pathway** for streaming data processing and a demand-based pricing model combining occupancy, queue length, traffic conditions, vehicle type, and special days.

---

## 📘 Table of Contents

- [Overview](#overview)
- [Step-by-Step Procedure](#step-by-step-procedure)
- [Pricing Model Formulae](#pricing-model-formulae)
- [Dependencies](#dependencies)
- [How to Run](#how-to-run)
- [Notes](#notes)
- [Future Enhancements](#future-enhancements)

---

## 🟢 Overview

Urban parking lots experience variable demand depending on time of day, traffic, special events, and vehicle mix.  
This project simulates ingesting parking lot status in **real time** and dynamically calculating a price per lot per day.

---

## 🛠 Step-by-Step Procedure

### ✅ **Step 1 – Data Cleaning**
- Combined `LastUpdatedDate` and `LastUpdatedTime` into a single `Timestamp`
- Replaced text labels in numeric columns (`low`, `medium`, `high`, `average`) with numeric equivalents:
  - `low` → `0.2`
  - `medium` → `0.5`
  - `high` → `0.8`
  - `average` → `0.5`
- Mapped `VehicleType` (car, bike, truck) to numeric weights
- Converted all numeric columns to numeric types and filled missing values with `0`
- Sorted data by parking lot and timestamp

### ✅ **Step 2 – Schema Definition**
Defined a Pathway schema specifying types for all fields:

| Column             | Type   | Description                            |
|---------------------|--------|----------------------------------------|
| Timestamp           | string | Combined timestamp                     |
| SystemCodeNumber    | string | Parking lot identifier                 |
| Occupancy           | int    | Current occupancy                      |
| Capacity            | int    | Parking lot capacity                   |
| QueueLength         | int    | Vehicles in queue                      |
| TrafficLevel        | float  | Normalized traffic level               |
| IsSpecialDay        | int    | Flag for holiday/special event         |
| VehicleTypeWeight   | float  | Vehicle type numeric impact factor     |

### ✅ **Step 3 – Streaming Ingestion**
- Used `pw.demo.replay_csv` to simulate streaming input
- Input rate set to 500 rows/second

### ✅ **Step 4 – Timestamp Parsing**
- Parsed event time (`t`) and day (`day`) fields for aggregation

### ✅ **Step 5 – Tumbling Window Aggregation**
- Aggregated all records **per day per parking lot**
- Computed:
  - Average occupancy
  - Average queue length
  - Average traffic level
  - Average vehicle type weight
  - Max special day flag
  - Max capacity

### ✅ **Step 6 – Pricing Calculation**
- Calculated demand and price for each day-window (formulae below)

### ✅ **Step 7 – Execution**
- Ran the pipeline using:
