# Why do we need a Data Warehouse?

A **Data Warehouse** is built to provide a **centralized, consistent, and reliable source of data** for analytics and decision-making across an organization.

### Key reasons

#### 1. Data Consistency & Quality

- Integrates data from multiple sources
- Ensures **clean, standardized, and reliable data**
- Removes inconsistencies between systems

#### 2. Historical Data Storage

- Stores **time-variant (historical) data**
- Enables trend analysis and forecasting
- Example: revenue over last 5 years

#### 3. Verifiability (Trust in Data)

- Data is **governed and validated**
- Ensures users can trust reports and metrics

#### 4. Supportability (Managed System)

- Maintained by dedicated data engineering teams
- Ensures reliability, performance, and scalability

#### 5. Usability (Easy Access for Analytics)

- Structured in a way optimized for reporting (star schema, etc.)
- Easy for BI tools like dashboards and analytics platforms

#### 6. Enterprise-wide Single Source of Truth

- Provides a **centralized view of business data**
- Eliminates confusion from multiple conflicting systems



## Transactional DataBase Design (OLTP)

OLTP systems are designed for **fast, reliable, and consistent processing of daily business transactions**.

- Focus = **speed + accuracy + consistency**

### Comparison

| Feature    | OLTP                    | OLAP                      |
| ---------- | ----------------------- | ------------------------- |
| Purpose    | Transactions            | Analytics                 |
| Data type  | Current data            | Historical data           |
| Operations | Insert/Update/Delete    | Read + Aggregate          |
| Speed      | Very fast for small ops | Fast for complex queries  |
| Schema     | Normalized              | Star/Snowflake            |
| Example    | Banking system          | Data warehouse (FDI, ADW) |

In Datawarehousing, insert and update speed is not relevant because end user shouldnt insert into warehouse. 

# Dimensional Modelling

A design technique for databases intended to support end-user queries in data warehousing. 

It organizes data in a way that reflects **business questions**, not operational processes.

## Features

- Data maintenance performance is secondary
- Data is denormalized as needed to support reporting 
- The resulting model reflects the kind of questions the business wants to ask

It consists of 

- Facts ( What happened in numbers )
- Dimensions. ( The story around that numbers )

Fact is also called a measure, is a measurable metric which is described by the dimensions such as sales amount or ordering quantity.  Represents **measurable business events**

Dimension describe business event like the sale of product. Its descriptive information can be numeric, but typically its often text based. Provide **context and descriptive details** around facts

Example : 

​	Fact -> Numeric transaction, measurable 
​	Dimension -> The descriptive details that provide context to the fact such as : -
​				Where -> store location 
​				Who -> customer
​				What -> Product 
​				When -> Date & Time of Purchase
So, the fact table stores the amount/quantity while the dimension tables stores all the descriptive attributes that explain the transaction. 

There are usually many more dimensions than facts. Fact table points to all of dimension entities. 

### Example

#### Fact Table (Sales)

- Sales Amount = 5000
- Quantity = 2
- Customer_ID = 101
- Product_ID = 501
- Date_ID = 20240101

#### Dimension Tables

- Customer 101 → “Amit, Delhi”
- Product 501 → “Laptop, Dell”
- Date → “24-Apr-2026”
- Store → “Hyderabad Branch”

## Star Schema

### 1. Fact points directly to Dimension

The fact table is at the center and stores measurable business values like sales, revenue, or quantity. It directly connects to dimension tables using foreign keys to provide context for each transaction.

### 2. Dimension does not point to other dimension

Dimension tables are independent and do not connect to each other. Each dimension only connects to the fact table, which keeps the design simple and makes queries faster and easier.

### 3. Surrogate keys in Dimension

Dimension tables use surrogate keys, which are system-generated IDs created by the data warehouse instead of using source system keys. This ensures consistency, uniqueness, and helps handle changes in source data over time.

## The Snowflake Schema

### 1. Dimensions relate to other dimensions

In a snowflake schema, dimension tables are **normalized**, meaning one dimension can be split into multiple related tables.
 Example: Product → Category → Department

### 2. More joins → performance impact

Because dimensions are split, queries need **more joins** to fetch data. This can make queries **slower and more complex** compared to star schema.

## Surrogate Keys

Surrogate keys are **artificially generated IDs** (usually integers) used in dimension tables. These are created by the **data warehouse**, not source systems. Used to **uniquely identify each row.** Stable even if source data changes

### Why ??

When you want to maintain history in dimensions **(Slowly Changing Dimensions)**, the problem is that the **business key (like Customer_ID)** stays the same even when details change. If you use that as the primary key, every update will overwrite the old data, so you lose history.

That’s why **surrogate keys** are used. Instead of updating the same row, the data warehouse creates a **new row with a new surrogate key** whenever something changes. This allows multiple versions of the same entity to exist.

So the idea is simple:
 same business key → multiple rows → different surrogate keys → history preserved.

## Grain

Level of detail a fact row represents.

## Conformed Dimension

A **conformed dimension** is a dimension that is **shared across multiple fact tables** and has the **same structure, meaning, and values everywhere**.

For example, a **Date dimension** used in both Sales and Inventory should have the same definition of day, month, year. Similarly, a Customer or Product dimension should be consistent across different datasets.

The key idea is consistency — if different teams use the same dimension, they should get **the same results for the same query**.

Example:
 Sales team and Inventory team both use the same **Date dimension**.

- Sales query → total revenue in Jan
- Inventory query → stock levels in Jan

Results are different because facts are different, but **“Jan”, “Year”, “Month” are defined the same way**. So there’s no confusion like:

- One team treating fiscal year differently
- Another using a different calendar

So the consistency is in:

- structure
- attributes
- business meaning

Not in the actual numbers.



## Slowly Changing Dimension

Slowly Changing Dimensions (SCD) is a concept used to handle changes in dimension data over time in a data warehouse. Since attributes like customer address or product category can change, SCD defines how those changes are stored.

Type 1: The existing value is overwritten with the new value. No history is maintained. This is the simplest approach and is used when old data is not important.

Type 2: A new row is created whenever a change happens. The old row is preserved, so full history is maintained. This usually involves a new surrogate key and sometimes additional columns like start date and end date.

Type 3: Only limited history is stored. Typically, the current and previous values are kept in the same row using separate columns. Older history is lost.

In short, SCD is about deciding whether to overwrite data, keep full history, or keep only partial history when dimension data changes.

## Kimball Methodology

Kimball methodology is a **bottom-up approach to data warehousing** focused on building data marts for specific business areas like sales or finance and then integrating them.

It is based on **dimensional modeling**, where data is organized into **fact tables (metrics)** and **dimension tables (context)**, usually using a star schema for fast querying.

A key concept is **conformed dimensions**, which ensures consistency across different data marts so they can work together as a unified system.

It also emphasizes **business-driven design**, meaning the model is built based on business questions rather than source systems.

In short, Kimball is about building a scalable data warehouse using dimensional models that are simple, fast, and aligned with business needs.

# FDI (Fusion Data Intelligence)

**Fusion Data Intelligence (FDI)** is a SaaS-based, packaged analytics platform that combines business data from multiple enterprise sources and applies analytics and AI capabilities on top of it. It enables users to gain insights and make data-driven business decisions.

It is built using multiple Oracle cloud products integrated together into a unified analytics solution.

## Core Components of FDI

#### 1. Prebuilt Data Pipeline

- Extracts data from **Oracle Fusion Cloud Applications** (ERP, HCM, SCM, etc.)
- Performs data transformation and loading (ETL/ELT)
- Loads the processed data into **Oracle Autonomous Data Warehouse (ADW)**

#### 2. Oracle Autonomous Data Warehouse (ADW)

- Central data repository for FDI
- Stores curated and integrated business data
- Optimized for analytics workloads and scalability

#### 3. Oracle Analytics Cloud (OAC)

- Analytics and visualization layer on top of ADW
- Builds interactive dashboards, reports, and visual insights
- Supports ad-hoc querying and self-service analytics

#### 4. Prebuilt Content Layer

FDI comes with ready-to-use business content such as:

- KPIs (Key Performance Indicators)
- Metrics
- Reports
- Dashboards

NOTE : Users can start using FDI immediately with built-in reports and metrics to get business insights, without needing to build custom dashboards. But if their business needs are more specific, they can create custom reports and analytics.

Here are the **key AI capabilities:**

- **Predictive Analytics** – forecasts future outcomes (sales, demand, churn, etc.)
- **Anomaly Detection** – identifies unusual patterns or outliers in data
- **Natural Language Query (NLQ)** – allows users to ask questions in plain English
- **Automated Insights** – automatically highlights trends and key drivers in data
- **Recommendations** – suggests actions based on patterns (next best action, focus areas)