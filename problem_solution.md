# 🚗 CityRide — The Data Challenge

## 1. Business Context

CityRide is a growing ride-booking company that wants to bring information from its **driver network, ride-booking application, and customer support team** into one central data platform.

The platform will help different teams understand:

* Ride demand
* Service performance
* Ride lifecycle information

However, the source systems have different **freshness, update, and data-quality requirements**.

The goal is to recommend an appropriate **data ingestion strategy** and show how the identified problems should be handled.

---

# 2. Source Systems

| **Source System**         | **Information**                         |
| ------------------------- | --------------------------------------- |
| Driver Network            | Driver information                      |
| Ride-booking Application  | Ride lifecycle information              |
| Customer Support Team     | Customer support information            |
| Customer / Driver Updates | Updated customer and driver information |

Customers and drivers can also update their information through the applications.

---

# 3. Problems & Proposed Solutions

## 3.1 Ingestion Strategy

---

## Problem 1 — Different Freshness Requirements

### Problem

Different teams need information at different levels of freshness, so a single ingestion method cannot efficiently meet all requirements.

### Proposed Solution

Use a **hybrid ingestion strategy** and select the appropriate approach for each type of information. **Batch, streaming, and incremental processing** are used according to how frequently the data needs to be available and how it changes.

### Flow

<img width="850" alt="Problem 1" src="https://github.com/user-attachments/assets/d682fc4a-9065-49fd-ad55-53af41cfe5e5" />

---

## Problem 2 — Driver Information Arrives at the End of the Day

### Problem

Driver information is usually shared at the end of the day, so it does not need to be continuously ingested throughout the day.

### Proposed Solution

Use **batch ingestion** to collect and process the driver information when the daily data is shared. This allows the complete set of driver records to be loaded together through a single batch process.

### Flow

<img width="850" alt="Problem 2" src="https://github.com/user-attachments/assets/bed6d0a8-b004-4180-9925-7b5cb7430c95" />

---

## Problem 3 — Ride Events Need to Be Available as They Happen

### Problem

Operations needs to know when rides are **booked, accepted, cancelled, or completed** as they happen, so delayed ingestion would reduce the freshness of operational information.

### Proposed Solution

Use **streaming ingestion** to continuously capture ride lifecycle events from the ride-booking application. Each event can enter the ingestion pipeline as it occurs, keeping the central platform updated with current ride information.

### Flow

<img width="850" alt="Problem 3" src="https://github.com/user-attachments/assets/1e806288-81ef-44e7-be70-81fcf36a27cd" />

---

## Problem 4 — Customer Support Information Needs to Be Ingested

### Problem

Customer support information needs to be brought into the central data platform so that support information can be used along with ride and service information.

### Proposed Solution

Use **periodic batch ingestion** to collect customer support records at defined intervals and load them into the central data platform. This brings support information into the platform in a controlled and consistent batch process.

### Flow

<img width="550" height= "500" alt="Problem 4" src="https://github.com/user-attachments/assets/6c4f1f47-acfa-4b93-9369-063c55eaef67" />

---

## Problem 5 — Customers and Drivers Can Update Their Information

### Problem

Customers and drivers can update information that was previously shared, so the platform needs to process changes without repeatedly processing unchanged information.

### Proposed Solution

Use **change detection and incremental processing** to identify new or changed customer and driver information. Only the changed information is processed, while unchanged information is skipped.

### Flow

<img width="850" alt="Problem 5" src="https://github.com/user-attachments/assets/2039f601-f9b6-48d1-9d43-487aba3e35fa" />

---

# 3.2 Data Handling

---

## Problem 6 — Some Information Arrives Late

### Problem

Some information arrives later than expected, which can cause the central platform to contain incomplete or outdated information.

### Proposed Solution

Use **late-data handling** to identify information that arrives after the expected processing time and incorporate it into the appropriate processing flow. This ensures that late information is not permanently missed.

### Flow

<img width="850" alt="Problem 6" src="https://github.com/user-attachments/assets/00358cfd-4c8a-449f-9949-b393d228d254" />

---

## Problem 7 — Some Records Are Incomplete

### Problem

Some incoming records are incomplete, which can introduce missing or unusable information into the central data platform.

### Proposed Solution

Apply **validation before further processing**. Valid records continue through the pipeline, while incomplete or invalid records are quarantined for investigation or correction.

### Flow

<img width="850" alt="Problem 7" src="https://github.com/user-attachments/assets/c780bc61-7ab6-4788-bbb7-5a1867f39d6b" />

---

## Problem 8 — Some Rides Appear More Than Once

### Problem

Some rides may appear more than once, which can cause the same ride to be represented as multiple records in the central platform.

### Proposed Solution

Use **duplicate detection and deduplication** before downstream processing. Duplicate ride records are identified and handled so that the same ride is not processed as multiple independent records.

### Flow

<img width="850" alt="Problem 8" src="https://github.com/user-attachments/assets/e5ed47a9-7a5a-480d-a844-93b2d4048a92" />

---

## Problem 9 — Drivers Correct Previously Shared Information

### Problem

Drivers may correct information that was already shared, so the central platform needs to reflect the latest corrected information.

### Proposed Solution

Detect the change and use **update/upsert processing** to update the existing record with the corrected information. This keeps the central platform aligned with the latest valid driver information.

### Flow
<img width="850" height="700" alt="image" src="https://github.com/user-attachments/assets/b5d2a376-8859-415e-aa68-ed1505cbc6d2" />


---

## Problem 10 — Source Information Changes After System Changes

### Problem

Information may be recorded differently after changes to the source system, which can affect how the incoming data is interpreted and processed.

### Proposed Solution

Use **schema validation and schema handling** to detect changes in the incoming data structure. Compatible changes can continue through the pipeline, while incompatible changes can be handled separately for transformation or investigation.

### Flow

<img width="850" alt="Problem 10" src="https://github.com/user-attachments/assets/b66015ba-71b2-43b2-9c41-641470afd591" />

---

# 4. Ingestion & Handling Decision Rationale

The ingestion and handling approach is selected based on the requirements described in the CityRide case.

| Source / Requirement | Approach | Reason |
|---|---|---|
| Driver Network | **Batch** | Driver information is usually shared at the end of the day, so it can be processed as a batch. |
| Ride-booking Application | **Streaming** | Ride events need to be available as they happen, so continuous event ingestion is appropriate. |
| Customer Support Team | **Periodic Batch** | Customer support records can be collected and processed at defined intervals as part of the ingestion pipeline. |
| Customer / Driver Updates | **Incremental Processing** | Information can change after it was previously shared, so processing can focus on new or changed information. |
| Incomplete Records | **Validation** | Identifies incomplete information before further processing. |
| Duplicate Rides | **Deduplication** | Prevents repeated ride records from being processed as separate records. |
| Corrected Information | **Update / Upsert** | Allows previously shared information to be updated when corrections are received. |
| Late Information | **Late-data Handling** | Allows information arriving later than expected to be incorporated appropriately. |
| Source Representation Changes | **Schema Handling** | Identifies changes in the incoming structure and determines how they should be handled. |

---

# 5. Overall Proposed Architecture

The individual solutions come together into the following ingestion flow:

<img width="1000" alt="Architecture" src="https://github.com/user-attachments/assets/e2f55f09-2536-4e33-af86-1f633b80273f" />

---

# 6. Recommended Approach

* **Batch** → Driver information shared daily
* **Streaming** → Ride events that need to be available as they happen
* **Periodic Batch** → Customer support information
* **Incremental Processing** → Customer and driver information that changes
* **Validation** → Incomplete or invalid records
* **Deduplication** → Repeated ride records
* **Update / Upsert** → Corrected information
* **Schema Handling** → Source representation changes
* **Late-data Handling** → Information arriving later than expected

---

# 7. Conclusion

CityRide should use a **hybrid ingestion strategy** rather than a single ingestion method for every source.

The proposed design matches the ingestion and handling approach to each requirement: **batch** for daily driver information, **streaming** for ride events that need to be available as they happen, **periodic batch** for customer support information, and **incremental processing** for information that changes.

The design also addresses the identified data-quality and source-change challenges through **validation, deduplication, update/upsert processing, late-data handling, and schema handling**.

This provides CityRide with an ingestion flow that is aligned with the requirements described in the case study.
