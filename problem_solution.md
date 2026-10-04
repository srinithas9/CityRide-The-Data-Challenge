CityRide — Problem & Solution

1. Different Freshness Requirements

Problem: Different teams need information at different levels of freshness, so a single ingestion method cannot efficiently meet all requirements.

Solution: Use a hybrid ingestion strategy with batch, streaming, and incremental processing based on how frequently information needs to be available and how it changes.

2. Driver Information Arrives at the End of the Day

Problem: Driver information is usually shared at the end of the day, so continuous ingestion is unnecessary.

Solution: Use batch ingestion to collect and process the daily driver information when it is shared.

3. Ride Events Need to Be Available as They Happen

Problem: Operations needs ride events such as booked, accepted, cancelled, and completed as they happen, so delayed ingestion would reduce operational freshness.

Solution: Use streaming ingestion to continuously capture ride lifecycle events from the ride-booking application.

4. Customer Support Information Needs to Be Ingested

Problem: Customer support information needs to be available in the central platform so it can be used along with ride and service information.

Solution: Use periodic batch ingestion to collect customer support records at defined intervals and load them into the central data platform.

5. Customers and Drivers Can Update Their Information

Problem: Customers and drivers can update previously shared information, so unchanged information should not be repeatedly processed.

Solution: Use change detection and incremental processing to identify new or changed information. Process changed information and skip unchanged information.

6. Some Information Arrives Late

Problem: Some information arrives later than expected, which can leave the central platform temporarily incomplete or outdated.

Solution: Use late-data handling to identify late-arriving information and incorporate it into the appropriate processing flow.

7. Some Records Are Incomplete

Problem: Some incoming records are incomplete, which can introduce missing or unusable information into the central platform.

Solution: Apply validation before further processing. Valid records continue, while incomplete or invalid records are quarantined for investigation or correction.

8. Some Rides Appear More Than Once

Problem: Some rides may appear more than once, which can cause duplicate ride records in the central platform.

Solution: Use duplicate detection and deduplication before downstream processing so the same ride is not processed as multiple independent records.

9. Drivers Correct Previously Shared Information

Problem: Drivers may correct information that was already shared, so the central platform needs to reflect the latest corrected information.

Solution: Detect the change and use update/upsert processing to update the existing record with the corrected information.

10. Source Information Changes After System Changes

Problem: Information may be recorded differently after source-system changes, which can affect how incoming data is interpreted and processed.

Solution: Use schema validation and schema handling to detect structural changes. Compatible changes can continue through the pipeline, while incompatible changes are handled separately for transformation or investigation.

Overall Problem → Solution Flow

CityRide Source Systems
          ↓
Identify Freshness & Change Requirements
          ↓
Choose Ingestion Approach
          ↓
Batch / Streaming / Periodic Batch / Incremental
          ↓
Validation & Data Handling
          ↓
Deduplication / Late Data / Updates / Schema Handling
          ↓
Central Data Platform
          ↓
Analytics & Reporting
