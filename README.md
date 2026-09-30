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

| **Source System**        | **Information**              |
| ------------------------ | ---------------------------- |
| Driver Network           | Driver information           |
| Ride-booking Application | Ride lifecycle information   |
| Customer Support Team    | Customer support information |

Customers and drivers can also update their information through the applications.

---

# 3. Problems & Proposed Solutions

## Problem 1 — Different Freshness Requirements

### Problem

Different teams need information at different levels of freshness.

### Proposed Solution

Use a **hybrid ingestion strategy**, where the ingestion method is selected according to the source's freshness and change requirements.

### Flow

```text
Different Freshness Requirements
              ↓
       Hybrid Ingestion
              ↓
    ┌─────────┼─────────┐
    ↓         ↓         ↓
  Batch    Streaming  Incremental
    ↓         ↓         ↓
Periodic   Real-time   Changed Data
Processing  Events      Only
```

---

## Problem 2 — Driver Information Arrives at the End of the Day

### Problem

Driver information is usually shared at the end of the day.

### Proposed Solution

Use **batch ingestion** because this information does not require continuous processing.

### Flow

```text
Driver Network
      ↓
Daily Driver Information
      ↓
Batch Ingestion
      ↓
Central Data Platform
```

---

## Problem 3 — Ride Events Need to Be Available as They Happen

### Problem

Operations needs to know when rides are **booked, accepted, cancelled, or completed** as they happen.

### Proposed Solution

Use **streaming ingestion** for ride lifecycle events.

### Flow

```text
Ride-booking Application
          ↓
       Ride Events
          ↓
      Streaming
          ↓
Central Data Platform
          ↓
Operations / Analytics
```

---

## Problem 4 — Customers and Drivers Can Update Their Information

### Problem

Customers and drivers can update their information after it was previously shared.

### Proposed Solution

Use **change detection and incremental processing** so that changed information is processed without repeatedly processing unchanged information.

### Flow

```text
Customer / Driver Information
              ↓
        Change Detection
              ↓
        ┌─────┴─────┐
        ↓           ↓
     Changed     Unchanged
        ↓           ↓
   Process      Skip / No
   Incrementally Reprocessing
```

---

## Problem 5 — Some Information Arrives Late

### Problem

Some information arrives later than expected.

### Proposed Solution

Use **late-data handling** so that late-arriving information can still be processed appropriately.

### Flow

```text
Incoming Information
          ↓
     Arrival Check
          ↓
    ┌─────┴─────┐
    ↓           ↓
 On Time       Late
    ↓           ↓
 Process     Late-data
 Normally     Handling
                ↓
             Process
```

---

## Problem 6 — Some Records Are Incomplete

### Problem

Some incoming records are incomplete.

### Proposed Solution

Validate records before processing them.

* Valid records continue through the pipeline.
* Incomplete or invalid records are quarantined for investigation or correction.

### Flow

```text
Incoming Record
      ↓
   Validation
      ↓
 ┌────┴─────┐
 ↓          ↓
Valid     Incomplete
 ↓          ↓
Process   Quarantine
```

---

## Problem 7 — Some Rides Appear More Than Once

### Problem

Some rides may appear more than once in the incoming information.

### Proposed Solution

Use **duplicate detection and deduplication** before the data is used downstream.

### Flow

```text
Incoming Ride Record
        ↓
  Duplicate Check
        ↓
   ┌────┴─────┐
   ↓          ↓
New Record   Duplicate
   ↓          ↓
 Process    Deduplicate
              ↓
        Avoid Duplicate
```

---

## Problem 8 — Drivers Correct Previously Shared Information

### Problem

Drivers may correct information that was already shared earlier.

### Proposed Solution

Detect the change and use **update/upsert processing** so the central platform reflects the corrected information.

### Flow

```text
Driver Information
        ↓
  Change Detection
        ↓
 Information Changed?
        ↓
       Yes
        ↓
   Update / Upsert
        ↓
Central Data Platform
```

---

## Problem 9 — Source Information Changes After System Changes

### Problem

Some information may be recorded differently after changes to the source system.

### Proposed Solution

Use **schema validation and schema handling** to identify compatible and incompatible changes.

### Flow

```text
Incoming Source Data
        ↓
  Schema Validation
        ↓
   ┌────┴──────────┐
   ↓               ↓
Compatible     Incompatible
   ↓               ↓
Transform /     Quarantine /
Process         Investigate
```

---

## Problem 10 — Unchanged Information Should Not Be Reprocessed

### Problem

CityRide wants to avoid repeatedly processing information that has not changed.

### Proposed Solution

Use **change detection and incremental processing** to identify only new or changed information.

### Flow

```text
Incoming Information
        ↓
  Change Detection
        ↓
   ┌────┴─────┐
   ↓          ↓
Changed    Unchanged
   ↓          ↓
Process      Skip
   ↓
Central Data Platform
```

---

# 4. Ingestion Decision Rationale

The ingestion approach is selected based on the requirements described in the CityRide case.

| Source / Requirement          | Ingestion Choice           | Reason                                                                                                 |
| ----------------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------ |
| Driver Network                | **Batch**                  | Driver information is usually shared at the end of the day, so continuous ingestion is not required.   |
| Ride-booking Application      | **Streaming**              | Ride events need to be available as they happen.                                                       |
| Customer / Driver Updates     | **Incremental Processing** | Information can change after it was previously shared, so processing can focus on changed information. |
| Unchanged Information         | **Change Detection**       | Avoids repeatedly processing information that has not changed.                                         |
| Incomplete Records            | **Validation**             | Identifies incomplete information before further processing.                                           |
| Duplicate Rides               | **Deduplication**          | Prevents repeated ride records from being processed as separate records.                               |
| Corrected Information         | **Update / Upsert**        | Allows previously shared information to be updated when corrections are received.                      |
| Late Information              | **Late-data Handling**     | Allows information arriving later than expected to be handled appropriately.                           |
| Source Representation Changes | **Schema Handling**        | Identifies changes in the incoming structure and determines how they should be handled.                |

---

# 5. Overall Proposed Architecture

The individual solutions come together into the following ingestion flow:
<img width="1536" height="1024" alt="Final_Architecture" src="https://github.com/user-attachments/assets/453d527c-019f-4df8-84d9-bf722235eda2" />

# 6. Final Recommendation

CityRide should use a **hybrid ingestion strategy** rather than using a single ingestion method for every source.

### Recommended approach

* **Batch** → Driver information shared daily
* **Streaming** → Ride events that need to be available as they happen
* **Incremental processing** → Customer and driver information that changes
* **Validation** → Incomplete or invalid records
* **Deduplication** → Repeated ride records
* **Update / Upsert** → Corrected information
* **Schema handling** → Source representation changes
* **Late-data handling** → Information arriving later than expected

This approach matches the ingestion method and handling mechanism to the requirements described in the CityRide case study.

---

# 7. Project Scope

This project focuses on:

* Understanding the source systems
* Identifying ingestion requirements
* Selecting appropriate ingestion approaches
* Handling changing information
* Handling data-quality issues
* Handling source representation changes
* Designing the proposed ingestion flow
* Explaining the reasoning behind the design

The design is based on the requirements provided in the **CityRide — The Data Challenge** case study.
