# TCS Wings Exam - Data Warehousing Study Notes

## 1. Introduction to Data Warehouses

### Data Warehouse Key Concepts
- **Definition**: A centralized repository that stores integrated data from multiple sources for analysis and reporting
- **Characteristics**:
  - Subject-oriented: Organized around major subjects (customers, products, sales)
  - Integrated: Data from various sources is consolidated
  - Time-variant: Historical data is maintained
  - Non-volatile: Data is stable and not frequently updated
- **Purpose**: Supports business intelligence activities, decision-making, and analytical reporting

### Importance of Data Warehouses
- Enables data-driven decision making
- Provides single source of truth for enterprise data
- Improves data quality and consistency
- Enhances query performance for analytical workloads
- Separates analytical processing from operational systems
- Supports historical analysis and trend identification
- Facilitates complex reporting and data mining

### Data Lake vs. Data Warehouse

| Aspect | Data Lake | Data Warehouse |
|--------|-----------|----------------|
| **Data Structure** | Raw, unstructured/semi-structured | Structured, processed |
| **Schema** | Schema-on-read | Schema-on-write |
| **Users** | Data scientists, advanced analysts | Business analysts, executives |
| **Processing** | ELT (Extract, Load, Transform) | ETL (Extract, Transform, Load) |
| **Cost** | Lower storage cost | Higher storage cost |
| **Flexibility** | High flexibility | Lower flexibility |
| **Purpose** | Exploration, machine learning | Reporting, BI |

### Data Mart vs. Data Warehouse

| Aspect | Data Mart | Data Warehouse |
|--------|-----------|----------------|
| **Scope** | Department/function specific | Enterprise-wide |
| **Size** | Smaller (GBs to TBs) | Larger (TBs to PBs) |
| **Complexity** | Less complex | More complex |
| **Implementation Time** | Faster (weeks to months) | Longer (months to years) |
| **Users** | Specific department | Entire organization |
| **Data Sources** | Limited sources or DW subset | Multiple enterprise sources |
| **Example** | Sales data mart, HR data mart | Complete organizational data |

### ETL Process

**ETL = Extract, Transform, Load**

1. **Extract**
   - Pull data from source systems (databases, APIs, files)
   - Handle various data formats
   - Capture incremental changes

2. **Transform**
   - Cleanse data (remove duplicates, fix errors)
   - Standardize formats and values
   - Apply business rules
   - Aggregate and calculate metrics
   - Join data from multiple sources
   - Handle missing values

3. **Load**
   - Insert transformed data into data warehouse
   - Can be full load or incremental load
   - Maintain data integrity and relationships

**Modern Alternative: ELT**
- Extract → Load → Transform
- Data loaded first, transformed in the warehouse
- Leverages cloud computing power

### Traditional Data Warehouse Options
- **Oracle Database**: Enterprise-grade, robust features
- **Microsoft SQL Server**: Windows-based, integrated with Microsoft ecosystem
- **IBM Db2 Warehouse**: Mainframe integration, enterprise scalability
- **Teradata**: High-performance analytics, parallel processing
- **SAP BW/4HANA**: In-memory computing, SAP integration
- **PostgreSQL**: Open-source option with analytical extensions

---

## 2. Dimension Modeling

### Dimensional Modeling
- **Definition**: Design technique optimized for querying and analyzing data in a data warehouse
- **Purpose**: Simplify complex database structures for better query performance
- **Key Benefits**:
  - Intuitive for business users
  - Fast query performance
  - Flexible for changing business requirements
  - Supports drill-down and roll-up operations

**Components**:
- **Fact Tables**: Contain measurable business metrics
- **Dimension Tables**: Provide context to facts

### Dimensional Modeling Alternatives

1. **Third Normal Form (3NF)**
   - Traditional relational modeling
   - Minimizes data redundancy
   - Complex queries required
   - Better for transactional systems

2. **Data Vault**
   - Auditable, scalable approach
   - Separates business keys, relationships, and descriptive data
   - Good for regulatory compliance

3. **Anchor Modeling**
   - Handles temporal data changes
   - Highly normalized
   - Complex but flexible

### Dimensional Modeling vs. Traditional Approach

| Aspect | Dimensional (Star/Snowflake) | Traditional (3NF) |
|--------|------------------------------|-------------------|
| **Purpose** | Analytical queries (OLAP) | Transactional processing (OLTP) |
| **Normalization** | Denormalized | Highly normalized |
| **Query Complexity** | Simpler joins | Complex multi-table joins |
| **Performance** | Faster for analytics | Faster for transactions |
| **Redundancy** | Some redundancy accepted | Minimal redundancy |
| **User-Friendliness** | Intuitive for business users | Technical, complex |
| **Maintenance** | Easier to modify | More complex changes |

### Dimensions

**Definition**: Descriptive attributes that provide context to facts

**Types of Dimensions**:

1. **Conformed Dimensions**
   - Shared across multiple fact tables
   - Ensures consistency (e.g., Date, Customer)

2. **Degenerate Dimensions**
   - Dimension key stored in fact table without separate dimension table
   - Example: Invoice number, Order ID

3. **Junk Dimensions**
   - Combines multiple low-cardinality flags/indicators
   - Reduces fact table columns

4. **Role-Playing Dimensions**
   - Same dimension used multiple times with different meanings
   - Example: Date dimension as Order Date, Ship Date, Delivery Date

5. **Slowly Changing Dimensions (SCD)**
   - **Type 0**: No changes allowed
   - **Type 1**: Overwrite old values (no history)
   - **Type 2**: Add new row with version (full history)
   - **Type 3**: Add new column (limited history)

**Common Dimensions**:
- Time/Date dimension
- Customer dimension
- Product dimension
- Location/Geography dimension
- Employee dimension

### Star Schema

**Structure**:
- Central fact table surrounded by dimension tables
- Direct relationship between fact and dimensions
- Denormalized dimension tables

**Characteristics**:
- Simple structure (star-shaped when visualized)
- Fewer joins required
- Faster query performance
- More storage space due to denormalization
- Easier for business users to understand

**Example**:
```
        Product Dim
              |
Customer -- FACT TABLE -- Date Dim
              |
        Location Dim
```

**Advantages**:
- Optimal query performance
- Simple to understand and navigate
- Easy to maintain

**Disadvantages**:
- Data redundancy in dimensions
- Higher storage requirements

### Snowflake Schema

**Structure**:
- Normalized version of star schema
- Dimension tables are broken into sub-dimensions
- Multiple levels of relationships

**Characteristics**:
- Dimension tables are normalized
- Reduces data redundancy
- More complex structure
- More joins required for queries

**Example**:
```
Product Category
      |
Product Subcategory
      |
Product Dim -- FACT TABLE -- Date Dim
                    |
              Store Dim
                    |
              City Dim
                    |
              State Dim
```

**Advantages**:
- Less storage space (normalized)
- Better data integrity
- Easier to maintain dimension hierarchies

**Disadvantages**:
- More complex queries (more joins)
- Slower query performance
- Less intuitive for users

**Star vs. Snowflake**:
- Star: Performance over storage
- Snowflake: Storage efficiency over performance

---

## 3. Create Your First Data Warehouse

### How to Install SQL Server

**Prerequisites**:
- Windows OS (or Linux for SQL Server on Linux)
- Minimum 6 GB hard disk space
- Minimum 1 GB RAM (4 GB recommended)

**Installation Steps**:
1. Download SQL Server Developer/Express Edition from Microsoft website
2. Run the installation executable
3. Choose installation type:
   - Basic (quick setup)
   - Custom (advanced configuration)
   - Download media only
4. Accept license terms
5. Select installation location
6. Choose instance configuration:
   - Default instance
   - Named instance
7. Configure server settings:
   - Authentication mode (Windows/Mixed)
   - Add administrators
8. Complete installation
9. Note down instance name and connection details

**Components to Install**:
- Database Engine Services
- SQL Server Replication (optional)
- Full-Text Search (optional)

### How to Connect SSMS to SQL Server

**SSMS = SQL Server Management Studio**

**Steps**:
1. Download and install SSMS from Microsoft website (separate from SQL Server)
2. Launch SSMS
3. Connection dialog appears:
   - **Server type**: Database Engine
   - **Server name**: 
     - Local: `localhost` or `.` or `(local)`
     - Named instance: `localhost\INSTANCENAME`
     - Remote: `ServerName` or `IPAddress`
   - **Authentication**:
     - Windows Authentication (default)
     - SQL Server Authentication (requires username/password)
4. Click "Connect"

**Troubleshooting**:
- Ensure SQL Server service is running
- Check firewall settings
- Verify TCP/IP protocol is enabled
- Confirm SQL Server Browser service is running (for named instances)

### How to Create a Data Warehouse Using SQL Server

**Step-by-Step Process**:

1. **Create Database**
```sql
CREATE DATABASE SalesDataWarehouse;
GO

USE SalesDataWarehouse;
GO
```

2. **Create Dimension Tables**
```sql
-- Date Dimension
CREATE TABLE DimDate (
    DateKey INT PRIMARY KEY,
    FullDate DATE,
    Year INT,
    Quarter INT,
    Month INT,
    MonthName VARCHAR(20),
    Day INT,
    DayOfWeek INT,
    DayName VARCHAR(20)
);

-- Product Dimension
CREATE TABLE DimProduct (
    ProductKey INT PRIMARY KEY IDENTITY(1,1),
    ProductID VARCHAR(50),
    ProductName VARCHAR(100),
    Category VARCHAR(50),
    SubCategory VARCHAR(50),
    Price DECIMAL(10,2)
);

-- Customer Dimension
CREATE TABLE DimCustomer (
    CustomerKey INT PRIMARY KEY IDENTITY(1,1),
    CustomerID VARCHAR(50),
    CustomerName VARCHAR(100),
    City VARCHAR(50),
    State VARCHAR(50),
    Country VARCHAR(50)
);
```

3. **Create Fact Table**
```sql
CREATE TABLE FactSales (
    SalesKey INT PRIMARY KEY IDENTITY(1,1),
    DateKey INT FOREIGN KEY REFERENCES DimDate(DateKey),
    ProductKey INT FOREIGN KEY REFERENCES DimProduct(ProductKey),
    CustomerKey INT FOREIGN KEY REFERENCES DimCustomer(CustomerKey),
    Quantity INT,
    UnitPrice DECIMAL(10,2),
    TotalAmount DECIMAL(15,2),
    Discount DECIMAL(10,2)
);
```

4. **Create ETL Procedures**
```sql
-- Example: Load Date Dimension
CREATE PROCEDURE LoadDimDate
AS
BEGIN
    DECLARE @StartDate DATE = '2020-01-01';
    DECLARE @EndDate DATE = '2025-12-31';
    
    WHILE @StartDate <= @EndDate
    BEGIN
        INSERT INTO DimDate VALUES (
            CAST(CONVERT(VARCHAR, @StartDate, 112) AS INT),
            @StartDate,
            YEAR(@StartDate),
            DATEPART(QUARTER, @StartDate),
            MONTH(@StartDate),
            DATENAME(MONTH, @StartDate),
            DAY(@StartDate),
            DATEPART(WEEKDAY, @StartDate),
            DATENAME(WEEKDAY, @StartDate)
        );
        
        SET @StartDate = DATEADD(DAY, 1, @StartDate);
    END
END;
```

5. **Create Indexes for Performance**
```sql
-- Create indexes on foreign keys
CREATE INDEX IX_FactSales_DateKey ON FactSales(DateKey);
CREATE INDEX IX_FactSales_ProductKey ON FactSales(ProductKey);
CREATE INDEX IX_FactSales_CustomerKey ON FactSales(CustomerKey);
```

6. **Set Up Backup and Maintenance**
- Configure regular backups
- Create maintenance plans
- Monitor disk space and performance

---

## 4. Modern Data Warehouses

### Cloud Data Warehouse

**Definition**: Data warehouse deployed and managed in the cloud, offering scalability and flexibility

**Key Features**:
- **Elastic Scalability**: Scale compute and storage independently
- **Pay-as-you-go**: Cost based on actual usage
- **Managed Service**: Vendor handles maintenance, updates, backups
- **Global Availability**: Access from anywhere
- **Automatic Updates**: Latest features without manual intervention
- **Built-in Security**: Encryption, access controls, compliance certifications

**Advantages**:
- No hardware investment
- Rapid deployment
- Automatic scaling
- High availability and disaster recovery
- Integration with cloud services
- Performance optimization handled by vendor

**Challenges**:
- Data transfer costs
- Network latency concerns
- Vendor lock-in
- Ongoing subscription costs
- Data sovereignty and compliance issues

### Cloud vs. On-Premises Data Warehouse

| Aspect | Cloud Data Warehouse | On-Premises Data Warehouse |
|--------|----------------------|----------------------------|
| **Initial Cost** | Low (no hardware) | High (servers, infrastructure) |
| **Operational Cost** | Subscription-based | Maintenance, staff, utilities |
| **Scalability** | Instant, elastic | Limited, requires hardware |
| **Deployment Time** | Hours to days | Weeks to months |
| **Maintenance** | Vendor-managed | In-house IT team |
| **Upgrades** | Automatic | Manual, time-consuming |
| **Performance** | Consistent, optimized | Depends on hardware investment |
| **Security** | Vendor-managed, shared responsibility | Full control, organization-managed |
| **Customization** | Limited | Extensive |
| **Disaster Recovery** | Built-in, multi-region | Requires separate investment |
| **Data Control** | Shared with vendor | Complete control |
| **Compliance** | Vendor certifications | Direct control for audits |

**When to Choose Cloud**:
- Rapid growth expected
- Limited IT infrastructure
- Need for global access
- Variable workloads
- Quick time-to-market

**When to Choose On-Premises**:
- Strict regulatory requirements
- Sensitive data concerns
- Predictable, stable workloads
- Existing infrastructure investment
- Full control requirements

### Cloud Data Warehouse Options

#### 1. **Amazon Redshift**
- **Provider**: AWS
- **Architecture**: Columnar storage, MPP (Massively Parallel Processing)
- **Features**:
  - Auto-scaling capabilities
  - Redshift Spectrum (query S3 data)
  - Integration with AWS ecosystem
  - Concurrency scaling
- **Best For**: AWS-centric organizations, large-scale analytics
- **Pricing**: Pay per node-hour

#### 2. **Google BigQuery**
- **Provider**: Google Cloud Platform
- **Architecture**: Serverless, fully managed
- **Features**:
  - Separation of compute and storage
  - Built-in machine learning (BigQuery ML)
  - Real-time analytics
  - Automatic scaling
  - Standard SQL support
- **Best For**: Ad-hoc queries, serverless architecture
- **Pricing**: Pay per query (data scanned)

#### 3. **Microsoft Azure Synapse Analytics**
- **Provider**: Microsoft Azure
- **Architecture**: Unified analytics platform
- **Features**:
  - Combines data warehousing and big data
  - Serverless and dedicated options
  - Integration with Power BI
  - Built-in security and compliance
  - Support for both SQL and Spark
- **Best For**: Microsoft ecosystem, hybrid analytics
- **Pricing**: Provisioned or serverless options

#### 4. **Snowflake**
- **Provider**: Snowflake Inc. (runs on AWS, Azure, GCP)
- **Architecture**: Multi-cluster shared data
- **Features**:
  - Complete separation of compute and storage
  - Zero-copy cloning
  - Time travel and data recovery
  - Cross-cloud data sharing
  - Automatic optimization
- **Best For**: Multi-cloud strategy, data sharing
- **Pricing**: Storage + compute credits

#### 5. **Oracle Autonomous Data Warehouse**
- **Provider**: Oracle Cloud
- **Architecture**: Self-driving, self-securing
- **Features**:
  - Automated management
  - Built-in security
  - Machine learning optimization
  - Integration with Oracle ecosystem
- **Best For**: Oracle database users, enterprise applications
- **Pricing**: OCPU and storage-based

#### 6. **IBM Db2 Warehouse on Cloud**
- **Provider**: IBM Cloud
- **Architecture**: SMP and MPP options
- **Features**:
  - In-memory processing
  - Oracle compatibility
  - Built-in analytics
  - Elastic scaling
- **Best For**: IBM ecosystem, enterprise migration
- **Pricing**: Subscription-based

**Selection Criteria**:
1. **Performance Requirements**: Query speed, data volume
2. **Cost Structure**: Storage vs. compute pricing
3. **Existing Infrastructure**: Cloud provider alignment
4. **Scalability Needs**: Growth expectations
5. **Integration Requirements**: BI tools, data sources
6. **Security and Compliance**: Industry regulations
7. **Skill Set**: Team expertise with platforms
8. **Features**: Specific capabilities needed (ML, real-time, etc.)

---

## 5. Data Warehouse Architecture

### DW Architecture Overview

**Why Architecture Matters:**
Every data warehouse is built on a layered architecture. Understanding which layer does what — and why — is critical for both design decisions and exam questions.

**The Core Flow:**
```
Source Systems
      ↓
  Staging Area   ← Temporary holding zone
      ↓
  ODS (optional) ← Near-real-time operational view
      ↓
  Data Warehouse ← Integrated, historical, analytical
      ↓
   Data Marts    ← Department-specific subsets
      ↓
BI / Reporting Tools
```

---

### 3-Tier Data Warehouse Architecture ⭐

**The standard architectural model for enterprise data warehouses.**

#### Tier 1 — Bottom Tier (Data Sources + Staging)

**Data Sources:**
- OLTP databases (Oracle, SQL Server, SAP)
- Flat files (CSV, Excel, XML, JSON)
- External data (APIs, third-party feeds, web data)
- Legacy systems (mainframe, COBOL files)

**Staging Area:**
- Temporary storage area sitting between sources and the warehouse
- Covered in detail in its own section below

**Key Point:** Raw data lands here first before any transformation

---

#### Tier 2 — Middle Tier (Data Warehouse Server)

**What It Is:**
- The core relational database engine storing integrated, cleansed, historical data
- Organized using dimensional modeling (star/snowflake schemas)
- Optimized for analytical queries, not transactions

**What Happens Here:**
- Data is transformed, validated, and integrated from staging
- Business rules are applied
- Historical records are maintained (SCD handling)
- Fact and dimension tables reside here

**Technologies Used:**
- Oracle, SQL Server, Teradata, Snowflake, BigQuery, Redshift

---

#### Tier 3 — Top Tier (Frontend / Presentation)

**What It Is:**
- The layer end users interact with
- Reporting tools, dashboards, ad-hoc query tools

**Tools at This Layer:**
- Tableau, Power BI, Looker (visualization)
- SSRS, Cognos, Business Objects (traditional reporting)
- Excel with ODBC connections
- Custom web applications

**Key Point:** Users never query the DW directly in well-designed systems — they query data marts or pre-built reports

---

### Inmon vs. Kimball Approach ⭐ (EXAM CRITICAL!)

**These are the two dominant philosophies for building a data warehouse. Expect scenario-based MCQs comparing them.**

---

#### Bill Inmon — "Top-Down" Approach

**Core Philosophy:**
Build a single, centralized, enterprise-wide data warehouse first. Data marts are derived from it afterward.

**Architecture Flow:**
```
Source Systems
      ↓
  Staging Area
      ↓
Enterprise Data Warehouse (EDW)
  [3NF normalized design]
      ↓
  ├── Sales Data Mart
  ├── HR Data Mart
  └── Finance Data Mart
```

**Key Characteristics:**
- Data warehouse designed in **3rd Normal Form (3NF)** — highly normalized
- Single source of truth at the enterprise level
- Data marts created as dependent subsets of the EDW
- Longer initial build time, but more consistent long-term

**Advantages:**
- High data consistency across the organization
- Single version of truth
- Easier to add new data marts over time
- Better for regulatory and compliance reporting

**Disadvantages:**
- Long time to first delivery (months to years)
- Complex to design and build
- Higher upfront cost
- Requires strong data governance from the start

**Best For:** Large enterprises, government organizations, highly regulated industries

---

#### Ralph Kimball — "Bottom-Up" Approach

**Core Philosophy:**
Build dimensional data marts first. The data warehouse is simply the union of all data marts.

**Architecture Flow:**
```
Source Systems
      ↓
  Staging Area
      ↓
  ├── Sales Data Mart    ← Built first
  ├── HR Data Mart       ← Built next
  └── Finance Data Mart  ← Built next
      ↓
  Data Warehouse Bus
  [Conformed Dimensions link them all]
```

**Key Characteristics:**
- Data marts use **Star Schema** (dimensional modeling)
- Conformed dimensions ensure consistency across marts
- The "data warehouse" is the collection of integrated data marts
- Faster delivery — deliver one mart at a time

**Advantages:**
- Faster time to value (first data mart in weeks)
- Business users involved from start
- Easier to understand and use
- Iterative delivery model

**Disadvantages:**
- Risk of inconsistency if conformed dimensions not managed well
- Can become difficult to integrate later if poorly planned
- Less suitable for complex enterprise-wide reporting initially

**Best For:** Smaller organizations, agile environments, departmental projects, fast business value delivery

---

#### Inmon vs. Kimball — Side-by-Side Comparison

| Aspect | Inmon (Top-Down) | Kimball (Bottom-Up) |
|--------|------------------|---------------------|
| **Starting Point** | Enterprise DW first | Data marts first |
| **Design** | 3NF (normalized) | Star Schema (dimensional) |
| **Delivery Speed** | Slow (full DW before first report) | Fast (one mart at a time) |
| **Complexity** | Higher | Lower |
| **Consistency** | Very high (single EDW) | Good (via conformed dims) |
| **Cost** | Higher upfront | Lower upfront, incremental |
| **Flexibility** | Less flexible early on | More flexible, iterative |
| **Best For** | Large enterprise, compliance | Agile teams, quick value |
| **Common Term** | Corporate Information Factory (CIF) | Dimensional Data Warehouse / Bus Architecture |

**Exam Tip:** The keyword "conformed dimensions" almost always points to Kimball. The keyword "enterprise data warehouse in 3NF" almost always points to Inmon.

---

### Staging Area ⭐

**Definition:** A temporary intermediate storage area where raw data from source systems is loaded before transformation and loading into the data warehouse.

**Think of it as:** A loading dock — goods arrive here, get inspected and sorted, then moved to the warehouse floor.

---

#### Why Staging Area is Needed

**Reason 1: Isolate Source Systems**
- Source OLTP systems are busy processing transactions
- Extracting large volumes during business hours degrades performance
- Staging lets you extract during off-peak hours and work at your own pace

**Reason 2: Data Quality and Validation**
- Validate data before it reaches the warehouse
- Bad data rejected or corrected in staging — not in the DW
- Easier to debug and fix issues without affecting production

**Reason 3: Handle Multiple Sources**
- Data from different systems arrives at different times and formats
- Staging consolidates everything before integration

**Reason 4: Recovery and Restart**
- If ETL fails midway, you don't need to re-extract from source
- Re-run the transformation step using already-extracted staging data

**Reason 5: Auditing and Compliance**
- Keep a copy of raw source data for audit purposes
- Prove what data arrived, when, and from where

---

#### Staging Area Characteristics

- **Temporary:** Data typically retained only until successfully loaded into DW
- **No business logic:** Raw data, minimal transformation (just format normalization)
- **Non-queryable by end users:** Not for reporting — internal ETL use only
- **Separate schema/database:** Physically isolated from production DW

---

#### Staging Area Patterns

**Pattern 1: Persistent Staging Area**
- Data retained after load (for audit, reprocessing)
- Longer storage, higher disk usage
- Used in regulated industries

**Pattern 2: Transient Staging Area**
- Data deleted after successful load to DW
- Lower storage requirements
- Most common pattern

**Pattern 3: Incremental Staging**
- Only new/changed records land in staging
- Requires CDC (Change Data Capture) from source
- Most efficient for large, frequently updated sources

---

#### Staging Area vs. ODS vs. Data Warehouse

| Aspect | Staging Area | ODS | Data Warehouse |
|--------|-------------|-----|----------------|
| **Data** | Raw, unprocessed | Integrated, near-real-time | Integrated, historical |
| **Purpose** | ETL intermediate step | Operational reporting | Analytical reporting |
| **History** | Usually none (transient) | Limited (current state) | Full history |
| **Users** | ETL processes only | Operational teams | Analysts, executives |
| **Update Frequency** | Per ETL batch | Near real-time | Batch (daily/weekly) |
| **Transformation** | Minimal | Some | Full business rules |

---

### Operational Data Store (ODS) ⭐

**Definition:** An integrated, current-state database that consolidates data from multiple source systems in near real-time for operational reporting and short-term analysis.

**Think of it as:** A "current view" of the business — not historical like the DW, not raw like staging.

---

#### Why ODS Exists

**The Gap it Fills:**
- Source OLTP systems: Fast, current, but siloed per department
- Data Warehouse: Integrated but historical (yesterday's data at best)
- **ODS fills the middle:** Integrated + near real-time

**Use Case Example:**
```
Customer calls support with a complaint.

Support agent needs:
- Current order status (from Order system)
- Current account balance (from Finance system)
- Last 3 interactions (from CRM)

→ ODS has all this integrated in one place, updated every 15 minutes
→ DW would only have yesterday's snapshot
→ Querying 3 separate OLTP systems would be slow and complex
```

---

#### ODS Characteristics

- **Current state focus:** Maintains current or near-current data (not deep history)
- **Integrated:** Combines data from multiple source systems
- **Volatile:** Data updated frequently (minutes or hours)
- **Subject-oriented:** Like DW but focused on operations, not analysis
- **Normalized design:** Often 3NF, not star schema (it's operational, not analytical)
- **Short retention:** Typically 30–90 days, after which data moves to DW

---

#### ODS Types

**Type 1 — Synchronous (Real-Time)**
- Updated immediately when source changes
- Highest data freshness
- Complex to implement

**Type 2 — Asynchronous (Near Real-Time)**
- Updated in mini-batches (every 15–60 minutes)
- Good balance of freshness and complexity
- Most common in practice

**Type 3 — Batch (Scheduled)**
- Updated in regular intervals (hourly, every few hours)
- Simplest to implement
- Less "current" but sufficient for many use cases

---

#### ODS vs. Data Warehouse

| Aspect | ODS | Data Warehouse |
|--------|-----|----------------|
| **Data Freshness** | Near real-time (minutes/hours) | Batch (daily/nightly) |
| **Data History** | Current/short-term (30–90 days) | Full history (years) |
| **Design** | Normalized (3NF) | Dimensional (star/snowflake) |
| **Primary Use** | Operational decisions | Strategic/analytical decisions |
| **Query Type** | Simple, current-state lookups | Complex analytical queries |
| **Update Frequency** | Continuous/frequent | Scheduled batch |
| **Audience** | Operations staff, support teams | Analysts, managers, executives |
| **Schema** | Entity-relationship | Star/snowflake |

---

#### When to Include ODS in Architecture

**Include ODS when:**
- Business needs near real-time operational reporting
- Multiple source systems need to be queried together
- Customer-facing operations need integrated current data
- Call center, customer support, banking transaction systems

**Skip ODS when:**
- Batch reporting is sufficient (overnight refreshes OK)
- All data comes from one source system
- Architecture simplicity is a priority
- Small organization with limited reporting needs

---

#### Complete Architecture with ODS

```
Source Systems (OLTP)
    ↓           ↓
Staging Area    Real-time feed
    ↓               ↓
    └───────────→  ODS  ← Near real-time, current state
                    ↓
              Data Warehouse ← Historical, integrated
                    ↓
               Data Marts   ← Department specific
                    ↓
               BI Tools     ← Tableau, Power BI
```

---

### Scenario-Based Questions

**Q1:** A company needs integrated customer data updated every 30 minutes for their support team. They already have a DW for monthly reports. What should they add?
- A) Another data mart
- B) ODS ✅
- C) More staging tables
- D) Second data warehouse

**Why?** ODS provides near real-time integrated data for operational use. DW is for historical/analytical reporting.

---

**Q2:** An architect designs a DW where the central EDW is in 3NF and data marts are derived from it. Which approach is this?
- A) Kimball
- B) Inmon ✅
- C) Data Vault
- D) Lambda Architecture

**Why?** Inmon = top-down, central EDW in 3NF, data marts derived after.

---

**Q3:** A team delivers a working Sales data mart in 6 weeks, then builds an HR data mart next. All use shared Date and Customer dimensions. Which approach?
- A) Inmon
- B) Kimball ✅
- C) ODS-first
- D) Data Vault

**Why?** Kimball = bottom-up, data marts first, conformed dimensions for consistency.

---

**Q4:** Your ETL job failed halfway through loading. You can restart from where it failed without re-extracting from the source. What made this possible?
- A) ODS
- B) Data Mart
- C) Staging Area ✅
- D) Conformed Dimensions

**Why?** Staging stores extracted raw data. ETL can restart transformation step without hitting the source again.

---

**Q5:** Which layer in the 3-tier architecture do end users interact with directly?
- A) Staging Area
- B) ODS
- C) Data Warehouse Server
- D) Frontend / BI Tools ✅

**Why?** Top tier (Tier 3) is where BI tools and reporting live — the user-facing layer.

---

## 6. Metadata Management

### What is Metadata?

**Definition:** "Data about data" — information that describes, explains, and provides context for your actual business data.

**Simple analogy:** Think of a library. The books are your data. The catalog (title, author, subject, location, date) is metadata — it describes the books without being the books themselves.

---

### Why Metadata Matters in a Data Warehouse

Without metadata, a data warehouse becomes a black box that users and developers can't trust or navigate. Metadata answers critical questions:
- Where did this data come from?
- What does this field actually mean?
- When was this data last updated?
- Who is responsible for this data?
- How was this value calculated?

---

### Types of Metadata ⭐

#### 1. Technical Metadata

**What it is:** Information about the physical structure and movement of data — the "how" and "where."

**Examples:**
- Table names, column names, data types, lengths
- Primary keys, foreign keys, indexes
- ETL mapping details (source column → target column)
- Transformation rules applied
- Load timestamps (when was last refresh?)
- Row counts per load
- Data lineage (full path from source to report)

**Who uses it:** ETL developers, DBAs, data engineers

**Where stored:** ETL tool repository (Informatica repository), database catalog, data dictionary

---

#### 2. Business Metadata

**What it is:** Information that makes data meaningful to non-technical business users — the "what" and "why."

**Examples:**
- Business definitions ("Revenue = Sales amount excluding taxes and discounts")
- Calculation rules ("Profit Margin = (Revenue - Cost) / Revenue × 100")
- Data ownership (HR department owns employee data)
- Allowed values and business rules
- Report names and their purposes
- KPI definitions and targets
- Glossary of business terms

**Who uses it:** Business analysts, managers, report consumers

**Where stored:** Business glossaries, data catalogs (Collibra, Alation, Apache Atlas)

---

#### 3. Operational Metadata

**What it is:** Information about ETL and DW operations — the "when" and "status."

**Examples:**
- When a workflow last ran (start time, end time)
- How many rows were extracted, transformed, loaded
- Session status (success, failed, aborted)
- Error logs and rejection counts
- Job dependencies (Job B runs after Job A succeeds)
- Schedule information

**Who uses it:** ETL operations team, DW administrators, IT support

**Where stored:** ETL tool logs (Informatica Workflow Monitor), scheduling systems, operational dashboards

---

### Technical vs. Business Metadata — Comparison

| Aspect | Technical Metadata | Business Metadata |
|--------|-------------------|-------------------|
| **Focus** | Physical structure and data movement | Business meaning and definitions |
| **Audience** | Developers, DBAs | Business analysts, end users |
| **Examples** | Table schemas, ETL mappings, lineage | Field definitions, KPI formulas |
| **Tool** | Data dictionary, ETL repository | Business glossary, data catalog |
| **Question Answered** | "Where is data stored / moved?" | "What does this data mean?" |

---

### Data Lineage ⭐

**Definition:** Tracks the complete journey of data from source system to final report or dashboard.

**Why it matters:**
- If a number in a report looks wrong, lineage tells you where to investigate
- Regulatory compliance (prove data origin and transformation)
- Impact analysis (if source changes, which reports are affected?)

**Example:**
```
Source: CRM System (CUSTOMERS table, column: ANNUAL_REVENUE)
    ↓
Staging: STG_CUSTOMERS.ANNUAL_REVENUE
    ↓
Transformation: Converted from USD to local currency
    ↓
DW: DIM_CUSTOMER.ANNUAL_REVENUE_LOCAL
    ↓
Report: "Customer Segment by Revenue" in Tableau
```

Lineage tells you every step between source and report.

---

### Data Dictionary vs. Business Glossary

**Data Dictionary:**
- Technical document
- Lists every table, column, data type, relationships
- Created and maintained by developers/DBAs
- Example entry: `CUST_ID | INT | Primary Key | NOT NULL | Range: 1–999999`

**Business Glossary:**
- Business document
- Lists business terms, their definitions, owners, related KPIs
- Created and maintained by business stakeholders
- Example entry: `"Active Customer" = Any customer with at least one purchase in the last 365 days`

**Together they form complete metadata coverage — technical details + business meaning.**

---

### Metadata Management Best Practices

✅ **Automate collection where possible**
- ETL tools (Informatica) automatically capture technical metadata
- Don't rely on manual documentation alone

✅ **Keep metadata current**
- Stale metadata is misleading
- Update when schemas change, ETL is modified, or business rules change

✅ **Centralize in a data catalog**
- Single place for users to find and trust information
- Tools: Apache Atlas, Collibra, Alation, AWS Glue Data Catalog

✅ **Define ownership**
- Every dataset should have a named owner responsible for accuracy
- Both technical owner (IT) and business owner (department)

✅ **Document transformations**
- Every calculated field or business rule must be documented
- Critical for audit, onboarding, and troubleshooting

---

### Scenario-Based Questions

**Q1:** A business analyst wants to know what "Net Revenue" means in the monthly sales report. Where would they find this?
- A) Technical Metadata / Data Dictionary
- B) Business Metadata / Business Glossary ✅
- C) Operational Metadata / ETL logs
- D) Staging Area documentation

**Why?** Business definitions and KPI formulas are business metadata, typically in a business glossary.

---

**Q2:** A DBA wants to find out what time the nightly ETL job ran last night and how many rows were loaded. Which metadata type?
- A) Technical Metadata
- B) Business Metadata
- C) Operational Metadata ✅
- D) Data Lineage

**Why?** ETL execution times, row counts, and job status are operational metadata.

---

**Q3:** After a schema change in the source system, the team needs to identify all downstream reports affected. Which metadata concept?
- A) Business Glossary
- B) Data Lineage ✅
- C) Operational Metadata
- D) Conformed Dimensions

**Why?** Data lineage maps the full path from source to report — essential for impact analysis.

---

**Q4:** Column names, data types, primary keys, and ETL mapping rules belong to which metadata type?
- A) Business Metadata
- B) Operational Metadata
- C) Technical Metadata ✅
- D) Descriptive Metadata

**Why?** Physical structure and ETL mapping details are technical metadata.

---

## Quick Reference Summary

### Key Exam Topics to Remember:

**Data Warehouse Fundamentals**:
- Subject-oriented, integrated, time-variant, non-volatile
- ETL vs. ELT processes
- Data lake (raw data) vs. Data warehouse (structured)
- Data mart (department) vs. Data warehouse (enterprise)

**Dimensional Modeling**:
- Fact tables (metrics) + Dimension tables (context)
- Star schema: Denormalized, faster queries
- Snowflake schema: Normalized, less storage
- SCD Types: 1 (overwrite), 2 (new row), 3 (new column)

**SQL Server Implementation**:
- Create database and tables with proper keys
- Use IDENTITY for surrogate keys
- Create foreign key relationships
- Build indexes on foreign keys for performance

**Cloud Solutions**:
- Elastic scalability and pay-as-you-go pricing
- Major platforms: Redshift, BigQuery, Synapse, Snowflake
- Cloud: Quick deployment, managed service
- On-premises: Full control, higher initial cost

**DW Architecture** *(NEW)*:
- 3-Tier: Sources → Staging/ODS → DW Server → BI Tools
- Staging Area: Temporary zone, isolates sources, enables ETL recovery
- ODS: Near real-time, integrated, current state — for operations (not analysis)
- Inmon (Top-Down): EDW in 3NF first → data marts derived after
- Kimball (Bottom-Up): Data marts first (star schema) → linked by conformed dimensions

**Metadata Management** *(NEW)*:
- Technical Metadata: Schemas, ETL mappings, data types, lineage (for developers)
- Business Metadata: Definitions, KPI formulas, glossary (for business users)
- Operational Metadata: ETL run times, row counts, job status (for operations)
- Data Lineage: Complete path from source to report — critical for impact analysis

**Good Luck with Your TCS Wings Exam!**
