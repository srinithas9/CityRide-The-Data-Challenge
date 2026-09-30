# CityRide — Problem & Solution

## 1. Different Freshness Requirements

**Problem:** Different teams need information at different levels of freshness.

**Solution:** Use a hybrid ingestion strategy.

**Flow:**

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

## 2. Driver Information Arrives at the End of the Day

**Problem:** Driver information is usually shared at the end of the day.

**Solution:** Use batch ingestion because continuous processing is not required.

**Flow:**

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

## 3. Ride Events Need to Be Available as They Happen

**Problem:** Operations needs to know when rides are booked, accepted, cancelled, or completed as they happen.

**Solution:** Use streaming ingestion for ride lifecycle events.

**Flow:**

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

## 4. Customers and Drivers Can Update Their Information

**Problem:** Customers and drivers can update information after it was previously shared.

**Solution:** Use change detection and incremental processing so that changed information can be processed without repeatedly processing unchanged information.

**Flow:**

```text
Customer / Driver Information
              ↓
        Change Detection
              ↓
       ┌──────┴──────┐
       ↓             ↓
    Changed       Unchanged
       ↓             ↓
 Incremental       Skip /
  Processing     No Reprocessing
       ↓
Central Data Platform
```

---

## 5. Some Information Arrives Late

**Problem:** Some information arrives later than expected.

**Solution:** Use late-data handling so that late-arriving information can still be processed appropriately.

**Flow:**

```text
Incoming Information
          ↓
     Arrival Check
          ↓
    ┌─────┴─────┐
    ↓           ↓
 On Time       Late
    ↓           ↓
 Process      Late-data
 Normally      Handling
                  ↓
               Process
```

---

## 6. Some Records Are Incomplete

**Problem:** Some incoming records are incomplete.

**Solution:** Validate records before processing.

* Valid records → continue processing
* Incomplete or invalid records → quarantine for investigation or correction

**Flow:**

```text
Incoming Record
      ↓
   Validation
      ↓
 ┌────┴─────────┐
 ↓              ↓
Valid        Incomplete
 ↓              ↓
Process      Quarantine
 ↓
Central Data Platform
```

---

## 7. Some Rides Appear More Than Once

**Problem:** Some rides may appear more than once.

**Solution:** Use duplicate detection and deduplication before downstream use.

**Flow:**

```text
Incoming Ride Record
        ↓
   Duplicate Check
        ↓
   ┌────┴─────┐
   ↓          ↓
  New       Duplicate
   ↓          ↓
Process    Deduplicate
   ↓          ↓
Central    Avoid Duplicate
Platform      Processing
```

---

## 8. Drivers Correct Previously Shared Information

**Problem:** Drivers may correct information that was already shared.

**Solution:** Detect the change and use update/upsert processing so the central platform reflects the corrected information.

**Flow:**

```text
Driver Information
        ↓
  Change Detection
        ↓
 Information Changed?
        ↓
   ┌────┴─────┐
   ↓          ↓
  Yes          No
   ↓           ↓
Update /     No Update
 Upsert
   ↓
Central Data Platform
```

---

## 9. Source Information Changes After System Changes

**Problem:** Some information may be recorded differently after changes to the source system.

**Solution:** Use schema validation and schema handling to identify compatible and incompatible changes.

**Flow:**

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
   ↓
Central Data Platform
```

---

## 10. Unchanged Information Should Not Be Reprocessed

**Problem:** CityRide wants to avoid repeatedly processing information that has not changed.

**Solution:** Use change detection and incremental processing to identify only new or changed information.

**Flow:**

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

## Overall Problem → Solution Flow

```text
CityRide Source Systems
          ↓
Identify Freshness & Change Requirements
          ↓
Choose Ingestion Approach
          ↓
Batch / Streaming / Incremental
          ↓
Validation & Data Handling
          ↓
Deduplication / Late Data / Updates / Schema Handling
          ↓
Central Data Platform
          ↓
Analytics & Reporting
```
