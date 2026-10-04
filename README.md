# 🚗 CityRide — The Data Challenge

## 1. Business Context.

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

<img width="1846" height="852" alt="Problem 1" src="https://github.com/user-attachments/assets/27e8c0af-f509-4451-8bc0-37a9e7ec92e6" />

---

## Problem 2 — Driver Information Arrives at the End of the Day

### Problem

Driver information is usually shared at the end of the day.

### Proposed Solution

Use **batch ingestion** because this information does not require continuous processing.

### Flow

<img width="1644" height="957" alt="Problem 2" src="https://github.com/user-attachments/assets/701f8bfe-a3d8-4403-a234-8e64bc6602a3" />

---

## Problem 3 — Ride Events Need to Be Available as They Happen

### Problem

Operations needs to know when rides are **booked, accepted, cancelled, or completed** as they happen.

### Proposed Solution

Use **streaming ingestion** for ride lifecycle events.

### Flow

<img width="1644" height="957" alt="Problem 3" src="https://github.com/user-attachments/assets/311ed244-33b6-47a3-bbc3-9fc17d530476" />

---

## Problem 4 — Customers and Drivers Can Update Their Information

### Problem

Customers and drivers can update their information after it was previously shared.

### Proposed Solution

Use **change detection and incremental processing** so that changed information can be identified and processed without repeatedly processing unchanged information.

### Flow

<img width="1644" height="957" alt="Problem 4" src="https://github.com/user-attachments/assets/d437c80a-981f-4d37-813a-f4741215f00a" />

---

## Problem 5 — Some Information Arrives Late

### Problem

Some information arrives later than expected.

### Proposed Solution

Use **late-data handling** so that late-arriving information can still be processed appropriately.

### Flow

<img width="1644" height="957" alt="Problem 5" src="https://github.com/user-attachments/assets/373d779c-576b-4478-96b5-5684c9f43e2a" />

---

## Problem 6 — Some Records Are Incomplete

### Problem

Some incoming records are incomplete.

### Proposed Solution

Validate records before processing them.

* Valid records continue through the pipeline.
* Incomplete or invalid records are quarantined for investigation or correction.

### Flow

<img width="1644" height="957" alt="Problem 6" src="https://github.com/user-attachments/assets/804cc2e8-510f-4397-810c-6d37c36ec113" />

---

## Problem 7 — Some Rides Appear More Than Once

### Problem

Some rides may appear more than once in the incoming information.

### Proposed Solution

Use **duplicate detection and deduplication** before the data is used downstream.

### Flow

<img width="1644" height="957" alt="Problem 7 (1)" src="https://github.com/user-attachments/assets/7cec095f-3006-4c0c-9531-8537a29bfe56" />

---

## Problem 8 — Drivers Correct Previously Shared Information

### Problem

Drivers may correct information that was already shared earlier.

### Proposed Solution

Detect the change and use **update/upsert processing** so the central platform reflects the corrected information.

### Flow

<img width="1589" height="990" alt="image" src="https://github.com/user-attachments/assets/a934d216-74a4-493e-9107-7d0c8459c1db" />

---

## Problem 9 — Source Information Changes After System Changes

### Problem

Some information may be recorded differently after changes to the source system.

### Proposed Solution

Use **schema validation and schema handling** to identify compatible and incompatible changes.

### Flow

<img width="1644" height="957" alt="Problem 9" src="https://github.com/user-attachments/assets/b59d63bb-13c2-460e-b081-931835b8d0a2" />

---

## Problem 10 — Unchanged Information Should Not Be Reprocessed

### Problem

CityRide wants to avoid repeatedly processing information that has not changed.

### Proposed Solution

Use **change detection and incremental processing** to identify unchanged information and avoid unnecessary processing.

### Flow

<img width="1589" height="990" alt="image" src="https://github.com/user-attachments/assets/eb1c317f-f575-4f14-a708-69c780cb0b3b" />

---

# 4. Ingestion & Handling Decision Rationale

The ingestion and handling approach is selected based on the requirements described in the CityRide case.

| Source / Requirement          | Approach                   | Reason                                                                                                 |
| ----------------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------ |
| Driver Network                | **Batch**                  | Driver information is usually shared at the end of the day, so continuous ingestion is not required.   |
| Ride-booking Application      | **Streaming**              | Ride events need to be available as they happen.                                                       |
| Customer / Driver Updates     | **Incremental Processing** | Information can change after it was previously shared, so processing can focus on changed information. |
| Unchanged Information         | **Change Detection**       | Identifies information that has not changed so it does not need to be processed again.                 |
| Incomplete Records            | **Validation**             | Identifies incomplete information before further processing.                                           |
| Duplicate Rides               | **Deduplication**          | Prevents repeated ride records from being processed as separate records.                               |
| Corrected Information         | **Update / Upsert**        | Allows previously shared information to be updated when corrections are received.                      |
| Late Information              | **Late-data Handling**     | Allows information arriving later than expected to be handled appropriately.                           |
| Source Representation Changes | **Schema Handling**        | Identifies changes in the incoming structure and determines how they should be handled.                |

---

# 5. Overall Proposed Architecture

The individual solutions come together into the following ingestion flow:

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/a2bccbc2-d56e-4597-96fe-176733a01c75" />


---

# 6. Recommended Approach

* **Batch** → Driver information shared daily
* **Streaming** → Ride events that need to be available as they happen
* **Incremental processing** → Customer and driver information that changes
* **Validation** → Incomplete or invalid records
* **Deduplication** → Repeated ride records
* **Update / Upsert** → Corrected information
* **Schema handling** → Source representation changes
* **Late-data handling** → Information arriving later than expected

---

# 7. Conclusion

CityRide should use a **hybrid ingestion strategy** rather than a single ingestion method for every source.

The proposed design matches the ingestion and handling approach to each requirement: **batch** for daily driver information, **streaming** for ride events that need to be available as they happen, and **incremental processing** for information that changes.

The design also addresses the identified data-quality and source-change challenges through **validation, deduplication, update/upsert processing, late-data handling, and schema handling**.

This provides CityRide with an ingestion flow that is aligned with the requirements described in the case study.
