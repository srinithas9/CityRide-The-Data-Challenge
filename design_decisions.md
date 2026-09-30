# CityRide — Design Decisions

## 1. Why a Hybrid Ingestion Strategy?

CityRide should not use one ingestion method for every source because the case study describes different freshness and change requirements.

Therefore, the proposed strategy combines:

* **Batch ingestion** for information shared periodically
* **Streaming ingestion** for events required as they happen
* **Incremental processing** for information that changes over time

This allows the ingestion approach to match the requirement of each source.

---

## 2. Why Batch for Driver Information?

The case states that driver information is usually shared at the end of the day.

Therefore, batch ingestion is appropriate because the information does not need to be continuously processed throughout the day.

```text
Driver Network
      ↓
Daily Information
      ↓
Batch Ingestion
      ↓
Central Data Platform
```

---

## 3. Why Streaming for Ride Events?

The case states that operations needs to know when rides are:

* Booked
* Accepted
* Cancelled
* Completed

as they happen.

Therefore, streaming ingestion is appropriate for ride lifecycle events because the requirement is for information to be available continuously as events occur.

```text
Ride-booking Application
          ↓
       Ride Events
          ↓
       Streaming
          ↓
Central Data Platform
```

---

## 4. Why Incremental Processing for Updates?

Customers and drivers can update their information through the applications.

Processing the entire dataset repeatedly would be unnecessary when only some information has changed.

Therefore, change detection and incremental processing should be used to focus processing on new or changed information.

```text
Information
     ↓
Change Detection
     ↓
 ┌───┴────┐
 ↓        ↓
Changed  Unchanged
 ↓        ↓
Process   Skip
```

---

## 5. Why Validation?

The case states that some records are incomplete.

Validation should happen before the records continue through the ingestion flow.

```text
Incoming Record
      ↓
   Validation
      ↓
 ┌────┴────────┐
 ↓             ↓
Valid       Incomplete
 ↓             ↓
Process     Quarantine
```

This prevents incomplete information from being treated as valid downstream data.

---

## 6. Why Deduplication?

The case states that some rides appear more than once.

Duplicate detection and deduplication are therefore required so that repeated ride records are not incorrectly treated as separate records.

```text
Incoming Ride
      ↓
Duplicate Check
      ↓
 ┌────┴─────┐
 ↓          ↓
New       Duplicate
 ↓          ↓
Process   Deduplicate
```

---

## 7. Why Update / Upsert?

Drivers may correct information that was already shared earlier.

The ingestion process therefore needs to recognize changed information and update the corresponding information in the central platform.

```text
Existing Information
        ↓
   Change Detected
        ↓
    Update / Upsert
        ↓
Central Data Platform
```

---

## 8. Why Late-data Handling?

The case states that some information arrives late.

Late-arriving information should not automatically be treated as invalid. Instead, the ingestion process should identify late information and handle it appropriately.

```text
Incoming Information
        ↓
   Arrival Check
        ↓
   ┌────┴────┐
   ↓         ↓
On Time     Late
   ↓         ↓
Process   Late-data
           Handling
```

---

## 9. Why Schema Handling?

The case states that some information is recorded differently after recent changes to the system.

Schema validation and handling help identify whether incoming information is compatible with the expected structure.

```text
Incoming Data
      ↓
Schema Validation
      ↓
 ┌────┴─────────┐
 ↓              ↓
Compatible   Incompatible
 ↓              ↓
Process      Quarantine /
             Investigate
```

---

## 10. Why Avoid Reprocessing Unchanged Information?

The case explicitly states that CityRide wants to avoid repeatedly processing information that has not changed.

Therefore, change detection and incremental processing should be used to identify information that actually needs processing.

```text
Incoming Information
        ↓
  Change Detection
        ↓
 ┌──────┴──────┐
 ↓             ↓
Changed     Unchanged
 ↓             ↓
Process        Skip
```

---

# Final Design Principle

The main design principle is:

> **Choose the ingestion and handling approach based on the freshness, change, and data-quality requirement described for each type of information.**

This results in a hybrid ingestion strategy consisting of:

| Requirement                         | Proposed Approach      |
| ----------------------------------- | ---------------------- |
| Periodic driver information         | Batch                  |
| Ride events as they happen          | Streaming              |
| Changed customer/driver information | Incremental processing |
| Incomplete records                  | Validation             |
| Duplicate rides                     | Deduplication          |
| Corrected information               | Update / Upsert        |
| Late information                    | Late-data handling     |
| Source representation changes       | Schema handling        |
| Unchanged information               | Change detection       |
