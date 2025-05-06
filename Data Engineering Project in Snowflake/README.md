# SWIGGY Data Pipeline | End To End Data Engineering Project In Snowflake

## Overview
This project implements a comprehensive end-to-end data pipeline in Snowflake for a food delivery application **Swiggy**, based on the **Swiggy_Orders_Dataset**. The pipeline processes data for various entities (e.g., Restaurant, Customer, Orders, Delivery) through three layers: **Stage**, **Clean**, and **Consumption**. It supports initial and delta loads from CSV files, enforces data integrity, tracks changes using streams, and implements Slowly Changing Dimension (SCD) Type 2 for historical data in the consumption layer. The pipeline includes a fact table for order items, a date dimension, and KPI views for revenue analysis. It ensures traceability, scalability, and compliance with PII policies.

## Dataset Description
The **Swiggy_Orders_Dataset** is organized into two main subfolders: `initial` and `delta`, each containing entity-specific subdirectories (e.g., `01-location-csv`, `02-restaurant-csv`). Each entity directory (e.g., `01.01-initial-load` for initial loads, or similar for delta loads) contains CSV files (e.g., `location-5rows.csv`) with raw data for ingestion. The dataset covers entities like Location, Restaurant, Customer, Orders, and more, with initial load files providing baseline data and delta load files capturing updates or upserts. The CSV files are structured to match the schema of the stage layer tables, ensuring seamless ingestion into the pipeline.

## Database and Schema Structure
The pipeline operates within the `sandbox` database and uses the following schemas:
1. **stage_sch**: Stores raw data from CSV files with all columns as `TEXT` for flexibility. Includes audit columns for traceability.
2. **clean_sch**: Transforms raw data into structured formats with appropriate data types and constraints. Does not support SCD Type 2.
3. **consumption_sch**: Stores dimensional data with SCD Type 2 for historical tracking, a fact table for order items, a date dimension, and KPI views for analytics.
4. **common**: Contains shared objects like file formats, stages, tags, and masking policies.

### Common Objects
- **File Format**: `stage_sch.csv_file_format` (CSV, comma-delimited, skip header, supports quoted fields).
- **Stage**: `stage_sch.csv_stg` (internal stage for CSV file ingestion).
- **Tag**: `common.pii_policy_tag` (values: `PII`, `PRICE`, `SENSITIVE`, `EMAIL`) for PII compliance.
- **Masking Policies**: Applied to sensitive fields (e.g., phone, email) to mask data (e.g., `** PII **`, `** EMAIL **`).

## Entities and Data Flow
The pipeline processes the following entities, each with stage, clean, and (where applicable) consumption layer tables, streams, and merge operations:

### 1. Restaurant
- **Stage**: `stage_sch.restaurant` (raw data, e.g., `restaurantid`, `name`, `cuisinetype`).
- **Clean**: `clean_sch.restaurant` (typed data, e.g., `restaurant_sk`, `pricing_for_two` as `NUMBER(10,2)`).
- **Consumption**: `consumption_sch.restaurant_dim` (SCD2, `restaurant_hk`, `eff_start_date`, `eff_end_date`).
- **Key Features**: PII tagging on `restaurant_phone`, geospatial data (`latitude`, `longitude`).

  ![image](https://github.com/user-attachments/assets/081eda51-e909-4b99-84e7-1a0741d80fa4)


### 2. Customer
- **Stage**: `stage_sch.customer` (e.g., `customerid`, `name`, `mobile`).
- **Clean**: `clean_sch.customer` (e.g., `customer_sk`, `dob` as `DATE`).
- **Consumption**: `consumption_sch.customer_dim` (SCD2, `customer_hk`).
- **Key Features**: PII tagging on `mobile`, `email`, `gender`, `dob`. Supports ~99,899 records.

  ![image](https://github.com/user-attachments/assets/f5fcd1c3-58d6-4272-a675-5f95a7e8fd98)


### 3. Customer Address
- **Stage**: `stage_sch.customeraddress` (e.g., `addressid`, `customerid`, `flatno`).
- **Clean**: `clean_sch.customer_address` (e.g., `customer_address_sk`, `pincode` as `STRING`).
- **Consumption**: `consumption_sch.customer_address_dim` (SCD2, `customer_address_hk`).
- **Key Features**: Detailed address components, `primary_flag` for primary address.

  ![image](https://github.com/user-attachments/assets/30f5480f-5f6f-4045-a527-2fff8ea90518)


### 4. Location
- **Stage**: `stage_sch.location` (e.g., `locationid`, `city`, `zipcode`).
- **Clean**: `clean_sch.restaurant_location` (e.g., `state_code`, `city_tier`).
- **Consumption**: `consumption_sch.restaurant_location_dim` (SCD2, `restaurant_location_hk`).
- **Key Features**: Enriches data with `state_code`, `is_union_territory`, `city_tier` (Tier-1, Tier-2, Tier-3).

  ![image](https://github.com/user-attachments/assets/7b4411d8-1888-4c56-9138-db056faee999)


### 5. Menu
- **Stage**: `stage_sch.menu` (e.g., `menuid`, `itemname`, `price`).
- **Clean**: `clean_sch.menu` (e.g., `menu_sk`, `price` as `DECIMAL(10,2)`).
- **Consumption**: `consumption_sch.menu_dim` (SCD2, `menu_dim_hk`).
- **Key Features**: Tracks `availability` as `BOOLEAN`, supports dietary classifications (`item_type`).

  ![image](https://github.com/user-attachments/assets/db0842a3-135e-40d1-8dac-e00c9a29d02c)


### 6. Orders (Transactional)
- **Stage**: `stage_sch.orders` (e.g., `orderid`, `customerid`, `totalamount`).
- **Clean**: `clean_sch.orders` (e.g., `order_sk`, `total_amount` as `DECIMAL(10,2)`).
- **Key Features**: Links to `customer` and `restaurant`, tracks `payment_method`.

### 7. Order Item (Transactional)
- **Stage**: `stage_sch.orderitem` (e.g., `orderitemid`, `menuid`, `quantity`).
- **Clean**: `clean_sch.order_item` (e.g., `order_item_sk`, `subtotal` as `DECIMAL(10,2)`).
- **Consumption**: `consumption_sch.order_item_fact` (fact table, `order_item_fact_sk`, links to multiple dimensions).
- **Key Features**: Links to `order` and `menu`, tracks item-level pricing. The fact table includes foreign keys to dimensions and measures like `quantity`, `price`, `subtotal`. It supports detailed analysis of order item metrics, such as revenue per item and delivery status tracking.

### 8. Delivery (Transactional)
- **Stage**: `stage_sch.delivery` (e.g., `deliveryid`, `orderid`, `deliverystatus`).
- **Clean**: `clean_sch.delivery` (e.g., `delivery_sk`, `delivery_date` as `TIMESTAMP`).
- **Key Features**: Links to `order`, `delivery_agent`, and `customer_address`.

### 9. Delivery Agent
- **Stage**: `stage_sch.deliveryagent` (e.g., `deliveryagentid`, `name`, `phone`).
- **Clean**: `clean_sch.delivery_agent` (e.g., `delivery_agent_sk`, `rating` as `NUMBER(4,2)`).
- **Consumption**: `consumption_sch.delivery_agent_dim` (SCD2, `delivery_agent_hk`).
- **Key Features**: Tracks `vehicle_type`, `status`, and `rating`.

  ![image](https://github.com/user-attachments/assets/9a068eaf-c9a6-4c5e-9299-9e985535b381)


### 10. Date Dimension
- **Consumption**: `consumption_sch.date_dim` (e.g., `date_dim_hk`, `calendar_date`, `year`, `month`).
- **Key Features**: Populated recursively from the minimum `order_date` in `clean_sch.orders` to the current date. Includes attributes like `quarter`, `week`, `day_name` for time-based analysis.

  ![image](https://github.com/user-attachments/assets/7ce82772-02f1-4af5-982b-e0e84f44ec65)



### 11. KPI Views
- **Consumption**: Five views in `consumption_sch` for revenue KPIs:
  - `vw_yearly_revenue_kpis`: Aggregates revenue, order count, and averages by year.
  - `vw_monthly_revenue_kpis`: Aggregates by year and month.
  - `vw_daily_revenue_kpis`: Aggregates by year, month, and day.
  - `vw_day_revenue_kpis`: Aggregates by year, month, and day of the week.
  - `vw_monthly_revenue_by_restaurant`: Aggregates by year, month, delivery status, and restaurant name.
- **Key Features**: Focus on delivered orders, provide metrics like `total_revenue`, `avg_revenue_per_order`, `max_order_value`.

## Fact Table Description
The `consumption_sch.order_item_fact` table is a central component of the consumption layer, designed to support detailed analytical queries for the food delivery app. Key aspects include:
- **Purpose**: Captures transactional data at the order item level, enabling analysis of revenue, quantities, and delivery metrics.
- **Structure**: Contains a surrogate key (`order_item_fact_sk`), natural keys (`order_item_id`, `order_id`), foreign keys to dimension tables (e.g., `customer_dim_key`, `restaurant_dim_key`), and measures (`quantity`, `price`, `subtotal`, `delivery_status`, `estimated_time`).
- **Foreign Key Constraints**: Enforces referential integrity with dimension tables (`customer_dim`, `customer_address_dim`, `restaurant_dim`, `restaurant_location_dim`, `menu_dim`, `delivery_agent_dim`, `date_dim`) to ensure data consistency.
- **Data Population**: Populated via a `MERGE` operation that joins streams from `clean_sch.order_item`, `orders`, and `delivery` with dimension tables, ensuring accurate mapping of hash keys (e.g., `customer_hk`, `menu_dim_hk`).
- **Analytical Use**: Supports KPI views by providing granular data for revenue calculations, order counts, and delivery status analysis, with measures like `subtotal` driving metrics such as `avg_revenue_per_item`.

  ![image](https://github.com/user-attachments/assets/76fb25d0-25e0-4071-8f2a-1ec6112936a5)

## ER Diagram - Consumption Schema
The Data Model follows Star Schema.

![Screenshot (79)](https://github.com/user-attachments/assets/249820db-2dc2-463a-8398-d2bc714dfff4)


## Pipeline Workflow
1. **Ingestion (Stage Layer)**:
   - **Initial Load**: CSV files from `Swiggy_Orders_Dataset/initial/<entity-dir>/<load-dir>/*.csv` (e.g., `01-location-csv/01.01-initial-load/location-5rows.csv`) are loaded into stage tables using `COPY INTO`.
   - **Delta Load**: Upsert files from `Swiggy_Orders_Dataset/delta/<entity-dir>/<load-dir>/*.csv` are loaded similarly.
   - **File Format**: Uses `stage_sch.csv_file_format` with `on_error = abort_statement` for data integrity.
   - **Audit Columns**: `_stg_file_name`, `_stg_file_load_ts`, `_stg_file_md5`, `_copy_data_ts` ensure traceability.

2. **Transformation (Clean Layer)**:
   - **Merge Operations**: Process changes from stage streams (e.g., `stage_sch.restaurant_stm`) into clean tables using `MERGE` statements.
   - **Data Type Conversion**: Uses `TRY_CAST`, `TRY_TO_TIMESTAMP`, etc., to safely convert `TEXT` to appropriate types (e.g., `NUMBER`, `TIMESTAMP_TZ`).
   - **Constraints**: Enforces `NOT NULL`, `UNIQUE`, and primary keys where applicable.
   - **Streams**: Track inserts, updates, and deletes for downstream processing.

3. **Dimensional and Fact Storage (Consumption Layer)**:
   - **Merge Operations**: Process changes from clean streams into dimensional tables (e.g., `consumption_sch.restaurant_dim`) and the fact table (`order_item_fact`).
   - **SCD Type 2**: Maintains historical data in dimensional tables with `eff_start_date`, `eff_end_date`, and `is_current` flags.
   - **Hash Keys**: Generates unique keys (e.g., `restaurant_hk`) using `hash(SHA1_hex(...))` for deduplication.
   - **Fact Table**: `order_item_fact` links to dimensions (`customer_dim`, `restaurant_dim`, etc.) via foreign keys and stores measures (`quantity`, `price`, `subtotal`).
   - **Date Dimension**: Populated with a recursive CTE to cover all dates from the earliest order to the current date.
   - **KPI Views**: Provide pre-aggregated revenue metrics for analytical reporting.

## Key Features
- **Scalability**: Handles large datasets (e.g., ~99,899 customer records) with efficient `MERGE` and stream-based processing.
- **Data Integrity**: Uses `TRY_CAST` and `TRY_TO_TIMESTAMP` to prevent casting errors, with `on_error = abort_statement` for load failures. Foreign key constraints in `order_item_fact` ensure referential integrity.
- **Traceability**: Audit columns track file metadata and load timestamps across all layers.
- **Change Tracking**: Append-only streams in stage and standard streams in clean layers ensure delta processing.
- **PII Compliance**: Tags sensitive fields (e.g., `mobile`, `email`, `restaurant_phone`) and applies masking policies.
- **SCD Type 2**: Enables historical analysis in dimensional tables for entities like Restaurant, Customer, and Delivery Agent.
- **Analytical Support**: `order_item_fact` and `date_dim` enable detailed analytics, enhanced by KPI views for revenue insights.
- **Enrichment**: Location entity includes derived attributes (e.g., `state_code`, `city_tier`) for analytical use.
- **Revenue KPIs**: Views provide yearly, monthly, daily, and restaurant-specific revenue metrics, focusing on delivered orders.

## Usage Instructions
1. **Setup**:
   - Execute `Create Database, Schema & Common Object.sql` to create the database, schemas, file format, stage, tags, and masking policies.
   - Ensure CSV files from `Swiggy_Orders_Dataset` are uploaded to `@stage_sch.csv_stg/initial/` and `@stage_sch.csv_stg/delta/`.

2. **Initial Load**:
   - Run `COPY INTO` commands for each entity (e.g., `Restaurant Entity.sql`) to load initial CSV files into stage tables.
   - Verify data using `SELECT * FROM stage_sch.<entity>` and `SELECT * FROM stage_sch.<entity>_stm`.

3. **Delta Load**:
   - Run `COPY INTO` commands for delta files (e.g., `@stage_sch.csv_stg/delta/restaurant/day-02-upsert-restaurant-delhi+NCR.csv`).
   - Check load history with:
     ```sql
     SELECT * FROM table(information_schema.copy_history(table_name=>'<ENTITY>', start_time=>dateadd(hours, -1, current_timestamp())));
     ```

4. **Processing**:
   - Execute `MERGE` statements to propagate changes from stage to clean and clean to consumption layers.
   - Run `Date Entity.sql` to populate `date_dim`.
   - Run `Order Item Fact.sql` to populate `order_item_fact`.
   - Create KPI views using `KPI Views.sql`.
   - Monitor streams (e.g., `SELECT * FROM clean_sch.<entity>_stm`) for pending changes.

5. **Verification**:
   - Query clean tables: `SELECT * FROM clean_sch.<entity>`.
   - Query dimensional tables: `SELECT * FROM consumption_sch.<entity>_dim`.
   - Query fact table: `SELECT * FROM consumption_sch.order_item_fact`.
   - Query KPI views: `SELECT * FROM consumption_sch.vw_yearly_revenue_kpis`.
   - Check stream counts: `SELECT COUNT(*) FROM clean_sch.<entity>_stm`.

## Notes
- **File Format**: Ensure `stage_sch.csv_file_format` is defined and matches CSV structure (e.g., comma-delimited, quoted fields).
- **CSV Structure**: Each entity’s CSV must match the column order in `COPY INTO` commands.
- **Delta Processing**: Avoid reprocessing delta files to prevent duplicates; use audit columns to track processed files.
- **SCD Type 2**: Dimensional tables may grow significantly due to historical records; monitor storage usage.
- **Performance**: Large datasets (e.g., Customer) benefit from Snowflake’s scalability, but optimize `MERGE` operations for frequent delta loads.
- **Fact Table Dependencies**: Ensure all dimension tables are populated before running `order_item_fact` merge to avoid missing foreign key references.
- **KPI Views**: Views are optimized for delivered orders; modify `WHERE` clauses if other statuses are needed.

## Entity Relationships
- **Restaurant** ↔ **Location** (via `location_id_fk`).
- **Customer** ↔ **Customer Address** (via `customer_id_fk`).
- **Orders** ↔ **Customer**, **Restaurant** (via `customer_id_fk`, `restaurant_id_fk`).
- **Order Item** ↔ **Orders**, **Menu** (via `order_id_fk`, `menu_id_fk`).
- **Delivery** ↔ **Orders**, **Delivery Agent**, **Customer Address** (via `order_id_fk`, `delivery_agent_id_fk`, `customer_address_id_fk`).
- **Menu** ↔ **Restaurant** (via `restaurant_id_fk`).
- **Delivery Agent** ↔ **Location** (via `location_id_fk`).
- **Order Item Fact** ↔ **Customer**, **Customer Address**, **Restaurant**, **Restaurant Location**, **Menu**, **Delivery Agent**, **Date** (via respective `_dim_key` fields).

This pipeline provides a robust foundation for analytics and reporting in a food delivery app, leveraging the **Swiggy_Orders_Dataset** to support operational, historical, and revenue-focused data needs.
