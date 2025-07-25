<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Complete Database Guide: From SQL to NoSQL - Corporate Production Grade Knowledge

## Table of Contents

1. [Introduction to Database Types](#introduction-to-database-types)
2. [In-Memory Databases](#in-memory-databases)
3. [SQL Databases](#sql-databases)
4. [NoSQL Databases](#nosql-databases)
5. [PostgreSQL Deep Dive](#postgresql-deep-dive)
6. [MongoDB Deep Dive](#mongodb-deep-dive)
7. [Cassandra Deep Dive](#cassandra-deep-dive)
8. [Corporate Database Comparison](#corporate-database-comparison)
9. [Advanced Partitioning Strategies](#advanced-partitioning-strategies)
10. [Advanced Indexing Techniques](#advanced-indexing-techniques)
11. [Production-Grade Best Practices](#production-grade-best-practices)
12. [Cheat Sheet](#cheat-sheet)

## Introduction to Database Types

Modern corporate environments require diverse database solutions to handle varying data patterns, scalability requirements, and performance needs. Understanding the fundamental differences between database types is crucial for making informed architectural decisions.

### Database Classification

| Database Type | Data Model | Consistency | Scalability | Use Cases |
| :-- | :-- | :-- | :-- | :-- |
| **Relational (SQL)** | Tables, Rows, Columns | ACID | Vertical | Transactions, Analytics |
| **Document (NoSQL)** | JSON-like Documents | Eventually Consistent | Horizontal | Content Management, APIs |
| **Wide-Column (NoSQL)** | Column Families | Tunable Consistency | Horizontal | Time-Series, IoT |
| **Key-Value (NoSQL)** | Key-Value Pairs | Eventually Consistent | Horizontal | Caching, Session Store |
| **In-Memory** | Various | Strong/Eventual | Both | Real-time Analytics, Gaming |

## In-Memory Databases

In-memory databases store data primarily in RAM rather than on disk, providing **microsecond response times** and eliminating disk I/O bottlenecks.[^1][^2]

### Core Characteristics

**Advantages:**

- **Ultra-low latency**: Microsecond read latency, single-digit millisecond write latency[^1]
- **High throughput**: Massive concurrent operations support[^1]
- **Predictable performance**: Consistent response times regardless of scale[^1]
- **Real-time processing**: Ideal for applications requiring immediate responses[^1]

**Challenges:**

- **Volatility**: Data loss risk during power failures[^2]
- **Cost**: RAM is more expensive than disk storage
- **Capacity limits**: Bounded by available memory


### Corporate Use Cases

#### Real-Time Analytics

```sql
-- Gaming leaderboards with instant updates
CREATE TABLE game_leaderboard (
    player_id VARCHAR(50),
    score BIGINT,
    last_updated TIMESTAMP,
    PRIMARY KEY (player_id)
);

-- Session management for web applications
CREATE TABLE user_sessions (
    session_id VARCHAR(100) PRIMARY KEY,
    user_id VARCHAR(50),
    login_time TIMESTAMP,
    last_activity TIMESTAMP,
    session_data JSON
);
```


#### Financial Trading Systems

```sql
-- Real-time stock prices
CREATE TABLE stock_prices (
    symbol VARCHAR(10),
    price DECIMAL(10,2),
    volume BIGINT,
    timestamp TIMESTAMP,
    PRIMARY KEY (symbol, timestamp)
);
```


### Popular In-Memory Solutions

| Solution | Type | Best For | Corporate Features |
| :-- | :-- | :-- | :-- |
| **Redis** | Key-Value | Caching, Sessions | Clustering, Persistence |
| **Apache Ignite** | Multi-Model | Complex Analytics | SQL Support, ACID |
| **SAP HANA** | Columnar | Enterprise Analytics | Advanced Analytics |
| **Amazon ElastiCache** | Managed Service | AWS Ecosystem | Multi-AZ, Backup |

## SQL Databases

SQL databases follow the relational model with structured data in tables, strong consistency guarantees, and standardized query language.[^3][^4]

### ACID Properties

**Atomicity**: Transactions are all-or-nothing
**Consistency**: Data integrity constraints are maintained
**Isolation**: Concurrent transactions don't interfere
**Durability**: Committed changes persist through failures

### When to Choose SQL

- **Complex relationships** between data entities
- **Strong consistency** requirements
- **Complex queries** with JOINs and aggregations
- **Regulatory compliance** (financial, healthcare)
- **Mature tooling** ecosystem needs


### SQL Database Types

#### OLTP (Online Transaction Processing)

```sql
-- E-commerce order processing
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INTEGER REFERENCES customers(id),
    order_date TIMESTAMP DEFAULT NOW(),
    total_amount DECIMAL(10,2),
    status VARCHAR(20) CHECK (status IN ('pending', 'processing', 'shipped', 'delivered'))
);

CREATE TABLE order_items (
    order_id INTEGER REFERENCES orders(order_id),
    product_id INTEGER REFERENCES products(id),
    quantity INTEGER NOT NULL,
    unit_price DECIMAL(10,2),
    PRIMARY KEY (order_id, product_id)
);
```


#### OLAP (Online Analytical Processing)

```sql
-- Sales analytics with dimensional modeling
CREATE TABLE sales_fact (
    sale_id BIGSERIAL PRIMARY KEY,
    date_key INTEGER REFERENCES date_dim(date_key),
    product_key INTEGER REFERENCES product_dim(product_key),
    customer_key INTEGER REFERENCES customer_dim(customer_key),
    quantity INTEGER,
    revenue DECIMAL(15,2),
    cost DECIMAL(15,2)
);

-- Analytical queries
SELECT 
    p.category,
    d.year,
    d.month,
    SUM(sf.revenue) as total_revenue,
    COUNT(DISTINCT sf.customer_key) as unique_customers
FROM sales_fact sf
JOIN product_dim p ON sf.product_key = p.product_key
JOIN date_dim d ON sf.date_key = d.date_key
WHERE d.year = 2024
GROUP BY p.category, d.year, d.month
ORDER BY total_revenue DESC;
```


## NoSQL Databases

NoSQL databases provide flexible schemas, horizontal scalability, and are optimized for specific data patterns.[^3][^4]

### NoSQL Categories

#### Document Databases

Store data as documents (JSON, BSON)

- **Examples**: MongoDB, CouchDB, Amazon DocumentDB
- **Use Cases**: Content management, catalogs, user profiles


#### Key-Value Stores

Simple key-value pairs

- **Examples**: Redis, DynamoDB, Riak
- **Use Cases**: Caching, session management, shopping carts


#### Wide-Column Stores

Column-family data model

- **Examples**: Cassandra, HBase, Google Bigtable
- **Use Cases**: Time-series data, IoT, analytics


#### Graph Databases

Nodes and relationships

- **Examples**: Neo4j, Amazon Neptune, ArangoDB
- **Use Cases**: Social networks, fraud detection, recommendations


### NoSQL vs SQL Trade-offs

| Aspect | SQL | NoSQL |
| :-- | :-- | :-- |
| **Schema** | Fixed, predefined | Flexible, evolving |
| **Scalability** | Vertical (scale-up) | Horizontal (scale-out) |
| **Consistency** | Strong (ACID) | Eventual (BASE) |
| **Query Complexity** | Complex JOINs, aggregations | Simple queries, denormalized |
| **Development Speed** | Slower initial setup | Rapid prototyping |
| **Data Integrity** | Built-in constraints | Application-level validation |

## PostgreSQL Deep Dive

PostgreSQL is an advanced open-source relational database with extensive features for enterprise applications.

### Advanced Data Types

```sql
-- JSON and JSONB for semi-structured data
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255),
    specifications JSONB,
    tags TEXT[],
    price_history DECIMAL(10,2)[]
);

-- Insert with complex data types
INSERT INTO products (name, specifications, tags, price_history) VALUES 
('Laptop', 
 '{"cpu": "Intel i7", "ram": "16GB", "ssd": "512GB"}',
 ARRAY['electronics', 'computers', 'business'],
 ARRAY[999.99, 1199.99, 1099.99]);

-- Query JSON data
SELECT name, specifications->>'cpu' as processor
FROM products 
WHERE specifications @> '{"ram": "16GB"}';
```


### Advanced Indexing in PostgreSQL

#### B-Tree Indexes (Default)

```sql
-- Standard B-tree index for equality and range queries
CREATE INDEX idx_orders_customer_date ON orders (customer_id, order_date);

-- Partial index for active orders only
CREATE INDEX idx_active_orders ON orders (order_date) 
WHERE status IN ('pending', 'processing');

-- Expression index
CREATE INDEX idx_customer_email_lower ON customers (LOWER(email));
```


#### Specialized Index Types

```sql
-- GIN index for full-text search
CREATE INDEX idx_products_search ON products 
USING GIN (to_tsvector('english', name || ' ' || description));

-- Query with full-text search
SELECT * FROM products 
WHERE to_tsvector('english', name || ' ' || description) 
@@ to_tsquery('english', 'laptop & gaming');

-- GiST index for geometric data
CREATE INDEX idx_locations_geo ON locations USING GIST (coordinates);

-- Hash index for equality queries
CREATE INDEX idx_products_sku_hash ON products USING HASH (sku);

-- BRIN index for large, naturally ordered tables
CREATE INDEX idx_sales_date_brin ON sales USING BRIN (sale_date);
```


### PostgreSQL Partitioning

#### Range Partitioning

```sql
-- Create partitioned table
CREATE TABLE sales_data (
    id BIGSERIAL,
    sale_date DATE NOT NULL,
    customer_id INTEGER,
    amount DECIMAL(10,2),
    region VARCHAR(50)
) PARTITION BY RANGE (sale_date);

-- Create partitions
CREATE TABLE sales_2024_q1 PARTITION OF sales_data
    FOR VALUES FROM ('2024-01-01') TO ('2024-04-01');

CREATE TABLE sales_2024_q2 PARTITION OF sales_data
    FOR VALUES FROM ('2024-04-01') TO ('2024-07-01');

-- Create indexes on partitioned table
CREATE INDEX idx_sales_customer ON sales_data (customer_id);
CREATE INDEX idx_sales_region ON sales_data (region);
```


#### Hash Partitioning

```sql
-- Hash partitioning for even distribution
CREATE TABLE user_activities (
    user_id BIGINT,
    activity_type VARCHAR(50),
    activity_date TIMESTAMP,
    metadata JSONB
) PARTITION BY HASH (user_id);

-- Create hash partitions
CREATE TABLE user_activities_0 PARTITION OF user_activities
    FOR VALUES WITH (MODULUS 4, REMAINDER 0);

CREATE TABLE user_activities_1 PARTITION OF user_activities
    FOR VALUES WITH (MODULUS 4, REMAINDER 1);
```


#### List Partitioning

```sql
-- List partitioning by region
CREATE TABLE regional_sales (
    sale_id BIGSERIAL,
    region VARCHAR(20),
    sale_date DATE,
    amount DECIMAL(10,2)
) PARTITION BY LIST (region);

CREATE TABLE sales_north_america PARTITION OF regional_sales
    FOR VALUES IN ('US', 'CA', 'MX');

CREATE TABLE sales_europe PARTITION OF regional_sales
    FOR VALUES IN ('UK', 'DE', 'FR', 'IT');
```


### Advanced PostgreSQL Features

#### Window Functions

```sql
-- Running totals and rankings
SELECT 
    customer_id,
    order_date,
    total_amount,
    SUM(total_amount) OVER (
        PARTITION BY customer_id 
        ORDER BY order_date 
        ROWS UNBOUNDED PRECEDING
    ) as running_total,
    ROW_NUMBER() OVER (
        PARTITION BY customer_id 
        ORDER BY total_amount DESC
    ) as order_rank
FROM orders;
```


#### Common Table Expressions (CTEs)

```sql
-- Recursive CTE for organizational hierarchy
WITH RECURSIVE employee_hierarchy AS (
    -- Base case: top-level managers
    SELECT employee_id, name, manager_id, 0 as level
    FROM employees 
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case: subordinates
    SELECT e.employee_id, e.name, e.manager_id, eh.level + 1
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT * FROM employee_hierarchy ORDER BY level, name;
```


### Performance Optimization

#### Query Planning and Analysis

```sql
-- Analyze query performance
EXPLAIN (ANALYZE, BUFFERS, VERBOSE) 
SELECT c.name, COUNT(o.order_id) as order_count
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE c.registration_date >= '2024-01-01'
GROUP BY c.customer_id, c.name
HAVING COUNT(o.order_id) > 5;

-- Update table statistics
ANALYZE customers;
ANALYZE orders;
```


#### Connection Pooling and Configuration

```sql
-- PostgreSQL configuration optimization
-- postgresql.conf settings for production

-- Memory settings
shared_buffers = 256MB                # 25% of RAM
effective_cache_size = 1GB           # 75% of RAM
work_mem = 4MB                       # Per-operation memory
maintenance_work_mem = 64MB          # For VACUUM, CREATE INDEX

-- WAL settings
wal_buffers = 16MB
checkpoint_completion_target = 0.9
max_wal_size = 1GB

-- Connection settings
max_connections = 200
```


## MongoDB Deep Dive

MongoDB is a document-oriented NoSQL database designed for flexibility and horizontal scalability.

### Document Model

```javascript
// Rich document structure
{
  "_id": ObjectId("..."),
  "customer_id": "CUST001",
  "name": "John Doe",
  "email": "john@example.com",
  "addresses": [
    {
      "type": "billing",
      "street": "123 Main St",
      "city": "New York",
      "zipcode": "10001",
      "country": "USA"
    },
    {
      "type": "shipping",
      "street": "456 Oak Ave",
      "city": "Boston",
      "zipcode": "02101",
      "country": "USA"
    }
  ],
  "orders": [
    {
      "order_id": "ORD001",
      "date": ISODate("2024-01-15"),
      "items": [
        {
          "product_id": "PROD001",
          "name": "Laptop",
          "quantity": 1,
          "price": 999.99
        }
      ],
      "total": 999.99,
      "status": "delivered"
    }
  ],
  "preferences": {
    "newsletter": true,
    "notifications": {
      "email": true,
      "sms": false
    }
  },
  "created_at": ISODate("2023-06-01"),
  "last_login": ISODate("2024-01-20")
}
```


### MongoDB Indexing Strategies

#### Single Field Indexes

```javascript
// Basic single field index
db.customers.createIndex({ "email": 1 })

// Compound index following ESR rule (Equality, Sort, Range)
db.orders.createIndex({ 
  "customer_id": 1,    // Equality
  "status": 1,         // Equality
  "order_date": -1     // Sort (descending)
})

// Sparse index (only for documents with the field)
db.products.createIndex({ "discount": 1 }, { sparse: true })
```


#### Advanced Index Types

```javascript
// Text index for full-text search
db.products.createIndex({ 
  "name": "text", 
  "description": "text", 
  "tags": "text" 
})

// Query with text search
db.products.find({ $text: { $search: "laptop gaming" } })

// Geospatial index
db.stores.createIndex({ "location": "2dsphere" })

// Query nearby stores
db.stores.find({
  location: {
    $near: {
      $geometry: { type: "Point", coordinates: [-73.9857, 40.7484] },
      $maxDistance: 1000  // meters
    }
  }
})

// Partial index with condition
db.orders.createIndex(
  { "customer_id": 1, "order_date": -1 },
  { partialFilterExpression: { "status": { $in: ["pending", "processing"] } } }
)
```


#### Compound Indexes and ESR Rule[^5]

```javascript
// ESR Rule: Equality, Sort, Range
// Good compound index design
db.sales.createIndex({
  "store_id": 1,      // Equality filter
  "category": 1,      // Equality filter  
  "sale_date": -1,    // Sort field
  "amount": 1         // Range filter
})

// Query that uses the index efficiently
db.sales.find({
  "store_id": "STORE001",
  "category": "electronics",
  "amount": { $gte: 100, $lte: 1000 }
}).sort({ "sale_date": -1 })
```


### MongoDB Sharding (Horizontal Partitioning)

#### Shard Key Selection

```javascript
// Configure sharding
sh.enableSharding("ecommerce")

// Range-based sharding
sh.shardCollection("ecommerce.orders", { "customer_id": 1 })

// Hash-based sharding for better distribution
sh.shardCollection("ecommerce.products", { "_id": "hashed" })

// Compound shard key for better query isolation
sh.shardCollection("ecommerce.user_activities", { 
  "user_id": 1, 
  "activity_date": 1 
})
```


#### Shard Key Patterns

```javascript
// Time-based data with compound shard key
{
  "_id": ObjectId("..."),
  "sensor_id": "SENSOR001",
  "timestamp": ISODate("2024-01-15T10:30:00Z"),
  "temperature": 23.5,
  "humidity": 65.2,
  "location": "Building-A-Floor-2"
}

// Shard by sensor_id and time bucket
sh.shardCollection("iot.sensor_data", { 
  "sensor_id": 1, 
  "timestamp": 1 
})
```


### MongoDB Aggregation Pipeline

```javascript
// Complex analytics with aggregation pipeline
db.orders.aggregate([
  // Stage 1: Match orders from last 30 days
  {
    $match: {
      "order_date": { 
        $gte: new Date(Date.now() - 30*24*60*60*1000) 
      },
      "status": { $in: ["completed", "shipped"] }
    }
  },
  
  // Stage 2: Unwind items array
  { $unwind: "$items" },
  
  // Stage 3: Lookup product details
  {
    $lookup: {
      from: "products",
      localField: "items.product_id",
      foreignField: "_id",
      as: "product_info"
    }
  },
  
  // Stage 4: Group by product category
  {
    $group: {
      _id: "$product_info.category",
      total_revenue: { $sum: { $multiply: ["$items.quantity", "$items.price"] } },
      total_quantity: { $sum: "$items.quantity" },
      unique_customers: { $addToSet: "$customer_id" },
      avg_order_value: { $avg: "$total_amount" }
    }
  },
  
  // Stage 5: Sort by revenue
  { $sort: { "total_revenue": -1 } },
  
  // Stage 6: Add calculated fields
  {
    $addFields: {
      customer_count: { $size: "$unique_customers" },
      revenue_per_customer: { $divide: ["$total_revenue", { $size: "$unique_customers" }] }
    }
  }
])
```


### Change Streams for Real-time Processing

```javascript
// Watch for changes in orders collection
const changeStream = db.orders.watch([
  { $match: { "fullDocument.status": "completed" } }
])

changeStream.on("change", (change) => {
  // Process completed orders in real-time
  if (change.operationType === "update") {
    console.log("Order completed:", change.fullDocument)
    // Trigger fulfillment process
    // Update inventory
    // Send notification
  }
})
```


## Cassandra Deep Dive

Apache Cassandra is a wide-column NoSQL database designed for massive scale, high availability, and linear scalability.[^6]

### Cassandra Data Model

#### Keyspace and Table Structure

```cql
-- Create keyspace with replication strategy
CREATE KEYSPACE ecommerce 
WITH REPLICATION = {
  'class': 'NetworkTopologyStrategy',
  'datacenter1': 3,
  'datacenter2': 2
};

USE ecommerce;

-- Time-series data table
CREATE TABLE user_activities (
    user_id UUID,
    activity_date DATE,
    activity_time TIMESTAMP,
    activity_type TEXT,
    session_id UUID,
    metadata MAP<TEXT, TEXT>,
    PRIMARY KEY ((user_id, activity_date), activity_time)
) WITH CLUSTERING ORDER BY (activity_time DESC);
```


### Cassandra Partitioning Strategy[^7][^6]

#### Understanding Partition Keys

The partition key determines **data distribution** across nodes and is crucial for performance.[^6]

```cql
-- Simple partition key
CREATE TABLE products (
    product_id UUID PRIMARY KEY,
    name TEXT,
    category TEXT,
    price DECIMAL
);

-- Composite partition key for better distribution
CREATE TABLE time_series_data (
    sensor_id UUID,
    date_bucket TEXT,  -- e.g., "2024-01"
    timestamp TIMESTAMP,
    temperature FLOAT,
    humidity FLOAT,
    PRIMARY KEY ((sensor_id, date_bucket), timestamp)
) WITH CLUSTERING ORDER BY (timestamp DESC);
```


#### Avoiding Hotspots

```cql
-- BAD: Using first character as partition key creates hotspots
CREATE TABLE word_index_bad (
    first_char TEXT,
    word TEXT,
    article_id UUID,
    frequency INT,
    PRIMARY KEY (first_char, word, article_id)
);

-- GOOD: Using full word as partition key
CREATE TABLE word_index_good (
    word TEXT,
    article_id UUID,
    frequency INT,
    title TEXT,
    PRIMARY KEY (word, article_id)
);
```

As explained in the Stack Overflow example, using the first character would create only 26 partitions, leading to **hot-spotting** where some partitions become much larger than others. Using the full word as the partition key provides better distribution.[^7]

#### Advanced Partition Key Strategies

```cql
-- Multi-tenant application with tenant isolation
CREATE TABLE tenant_data (
    tenant_id UUID,
    user_id UUID,
    data_type TEXT,
    created_at TIMESTAMP,
    data BLOB,
    PRIMARY KEY ((tenant_id, user_id), data_type, created_at)
) WITH CLUSTERING ORDER BY (data_type ASC, created_at DESC);

-- Time-bucketed data for better query performance
CREATE TABLE metrics (
    service_name TEXT,
    hour_bucket TEXT,     -- "2024-01-15-14" for hour-level bucketing
    timestamp TIMESTAMP,
    metric_name TEXT,
    value DOUBLE,
    PRIMARY KEY ((service_name, hour_bucket), timestamp, metric_name)
) WITH CLUSTERING ORDER BY (timestamp DESC, metric_name ASC);
```


### Cassandra Clustering Keys and Ordering

```cql
-- Order management with proper clustering
CREATE TABLE customer_orders (
    customer_id UUID,
    order_date DATE,
    order_id UUID,
    status TEXT,
    total_amount DECIMAL,
    items LIST<FROZEN<order_item>>,
    PRIMARY KEY (customer_id, order_date, order_id)
) WITH CLUSTERING ORDER BY (order_date DESC, order_id DESC);

-- User-defined type for order items
CREATE TYPE order_item (
    product_id UUID,
    product_name TEXT,
    quantity INT,
    unit_price DECIMAL
);
```


### Cassandra Indexing

#### Secondary Indexes

```cql
-- Secondary index on regular column
CREATE INDEX ON products (category);

-- Query using secondary index
SELECT * FROM products WHERE category = 'electronics';

-- Composite secondary index
CREATE INDEX ON user_activities (activity_type);
```


#### Materialized Views

```cql
-- Materialized view for different query pattern
CREATE MATERIALIZED VIEW products_by_category AS
SELECT category, product_id, name, price
FROM products
WHERE category IS NOT NULL AND product_id IS NOT NULL
PRIMARY KEY (category, product_id);

-- Now you can query by category efficiently
SELECT * FROM products_by_category WHERE category = 'electronics';
```


### Cassandra Consistency Levels

```cql
-- Configure consistency for reads and writes
-- Write with QUORUM consistency
INSERT INTO customer_orders (customer_id, order_date, order_id, status, total_amount)
VALUES (uuid(), '2024-01-15', uuid(), 'pending', 99.99)
USING CONSISTENCY QUORUM;

-- Read with LOCAL_QUORUM for better performance
SELECT * FROM customer_orders 
WHERE customer_id = ? AND order_date = ?
USING CONSISTENCY LOCAL_QUORUM;
```


### Cassandra Data Modeling Patterns

#### Event Sourcing Pattern

```cql
-- Event store table
CREATE TABLE event_store (
    aggregate_id UUID,
    event_version BIGINT,
    event_type TEXT,
    event_data TEXT,
    timestamp TIMESTAMP,
    PRIMARY KEY (aggregate_id, event_version)
) WITH CLUSTERING ORDER BY (event_version ASC);

-- Snapshot table for performance
CREATE TABLE snapshots (
    aggregate_id UUID PRIMARY KEY,
    snapshot_version BIGINT,
    snapshot_data TEXT,
    created_at TIMESTAMP
);
```


#### Time-Series Data Pattern

```cql
-- Metrics with automatic bucketing
CREATE TABLE system_metrics (
    service_name TEXT,
    metric_name TEXT,
    day_bucket DATE,
    timestamp TIMESTAMP,
    value DOUBLE,
    tags MAP<TEXT, TEXT>,
    PRIMARY KEY ((service_name, metric_name, day_bucket), timestamp)
) WITH CLUSTERING ORDER BY (timestamp DESC)
AND compaction = {
    'class': 'TimeWindowCompactionStrategy',
    'compaction_window_unit': 'DAYS',
    'compaction_window_size': 1
};
```


## Corporate Database Comparison

### Performance Characteristics

| Database | Read Performance | Write Performance | Scalability | Consistency |
| :-- | :-- | :-- | :-- | :-- |
| **PostgreSQL** | High (with proper indexing) | High (MVCC) | Vertical + Read Replicas | Strong (ACID) |
| **MongoDB** | High (memory-mapped) | Very High | Horizontal (Sharding) | Tunable |
| **Cassandra** | Very High | Exceptional | Linear Horizontal | Tunable (CAP) |
| **Redis (In-Memory)** | Exceptional | Exceptional | Horizontal (Cluster) | Strong/Eventual |

### Corporate Use Case Scenarios

#### E-commerce Platform Architecture

**PostgreSQL**: Order processing, inventory management, financial transactions

```sql
-- ACID transactions for order processing
BEGIN;
  INSERT INTO orders (customer_id, total_amount) VALUES (?, ?);
  UPDATE inventory SET quantity = quantity - ? WHERE product_id = ?;
  INSERT INTO payment_transactions (order_id, amount, status) VALUES (?, ?, 'pending');
COMMIT;
```

**MongoDB**: Product catalog, user profiles, content management

```javascript
// Flexible product catalog
{
  "_id": ObjectId("..."),
  "sku": "LAPTOP001",
  "name": "Gaming Laptop",
  "categories": ["electronics", "computers", "gaming"],
  "specifications": {
    "cpu": "Intel i7-12700H",
    "gpu": "RTX 3070",
    "ram": "16GB DDR4",
    "storage": "1TB NVMe SSD"
  },
  "pricing": {
    "base_price": 1299.99,
    "discounts": [
      {"type": "student", "percentage": 10},
      {"type": "bulk", "min_quantity": 5, "percentage": 15}
    ]
  },
  "availability": {
    "in_stock": true,
    "quantity": 50,
    "warehouses": ["US-WEST", "US-EAST"]
  }
}
```

**Cassandra**: User activity tracking, real-time analytics, session data

```cql
-- High-volume user activity tracking
CREATE TABLE user_events (
    user_id UUID,
    session_id UUID,
    event_date DATE,
    event_time TIMESTAMP,
    event_type TEXT,
    page_url TEXT,
    user_agent TEXT,
    ip_address INET,
    metadata MAP<TEXT, TEXT>,
    PRIMARY KEY ((user_id, event_date), event_time, session_id)
) WITH CLUSTERING ORDER BY (event_time DESC, session_id ASC);
```

**Redis**: Caching, session management, real-time features

```bash
# Session management
SETEX session:user123 3600 '{"user_id":123,"role":"customer","cart_items":5}'

# Product recommendation cache
ZADD trending:electronics 100 "laptop001" 95 "mouse002" 90 "keyboard003"

# Real-time notifications
LPUSH notifications:user123 '{"type":"order_shipped","order_id":"ORD001"}'
```


#### Financial Services Architecture

**PostgreSQL**: Core banking, regulatory reporting, audit trails

```sql
-- Double-entry bookkeeping with ACID guarantees
CREATE TABLE transactions (
    transaction_id UUID PRIMARY KEY,
    reference_number VARCHAR(50) UNIQUE,
    transaction_date TIMESTAMP NOT NULL,
    description TEXT,
    total_amount DECIMAL(15,2) NOT NULL,
    created_by INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE journal_entries (
    entry_id UUID PRIMARY KEY,
    transaction_id UUID REFERENCES transactions(transaction_id),
    account_id INTEGER REFERENCES chart_of_accounts(id),
    debit_amount DECIMAL(15,2) DEFAULT 0,
    credit_amount DECIMAL(15,2) DEFAULT 0,
    CHECK (debit_amount >= 0 AND credit_amount >= 0),
    CHECK (debit_amount = 0 OR credit_amount = 0)
);
```

**MongoDB**: Customer 360, document management, KYC data

```javascript
// Customer 360 view
{
  "_id": ObjectId("..."),
  "customer_id": "CUST789",
  "personal_info": {
    "first_name": "Jane",
    "last_name": "Smith",
    "date_of_birth": ISODate("1985-03-15"),
    "ssn_hash": "hashed_ssn_value",
    "addresses": [...]
  },
  "accounts": [
    {
      "account_number": "ACC001",
      "account_type": "checking",
      "balance": 2500.00,
      "status": "active",
      "opened_date": ISODate("2020-01-15")
    }
  ],
  "kyc_documents": [
    {
      "type": "drivers_license",
      "document_id": "DL123456",
      "verified": true,
      "verification_date": ISODate("2024-01-10")
    }
  ],
  "risk_profile": {
    "score": 650,
    "category": "medium",
    "last_updated": ISODate("2024-01-01")
  }
}
```

**Cassandra**: Transaction monitoring, fraud detection, audit logs

```cql
-- Real-time transaction monitoring
CREATE TABLE transaction_events (
    account_id UUID,
    event_date DATE,
    event_time TIMESTAMP,
    transaction_id UUID,
    amount DECIMAL,
    merchant_id TEXT,
    location TEXT,
    risk_score FLOAT,
    flags SET<TEXT>,
    PRIMARY KEY ((account_id, event_date), event_time)
) WITH CLUSTERING ORDER BY (event_time DESC);
```


### Multi-Database Corporate Architecture

```yaml
# Microservices with specialized databases
services:
  user-service:
    database: PostgreSQL
    purpose: User authentication, authorization
    
  product-catalog:
    database: MongoDB
    purpose: Product information, search
    
  order-processing:
    database: PostgreSQL
    purpose: Transactions, inventory
    
  analytics-service:
    database: Cassandra
    purpose: Real-time analytics, reporting
    
  cache-layer:
    database: Redis
    purpose: Session management, caching
    
  recommendation-engine:
    database: Neo4j
    purpose: Graph-based recommendations
```


## Advanced Partitioning Strategies

### PostgreSQL Partitioning Patterns[^8][^9]

#### Time-Based Partitioning with Automation

```sql
-- Create partitioned table with automatic partition creation
CREATE TABLE sales_data (
    id BIGSERIAL,
    sale_date DATE NOT NULL,
    customer_id INTEGER,
    product_id INTEGER,
    amount DECIMAL(10,2),
    region VARCHAR(50)
) PARTITION BY RANGE (sale_date);

-- Function to create monthly partitions
CREATE OR REPLACE FUNCTION create_monthly_partition(
    table_name TEXT,
    start_date DATE
) RETURNS VOID AS $$
DECLARE
    partition_name TEXT;
    end_date DATE;
BEGIN
    partition_name := table_name || '_' || to_char(start_date, 'YYYY_MM');
    end_date := start_date + INTERVAL '1 month';
    
    EXECUTE format('CREATE TABLE %I PARTITION OF %I 
                    FOR VALUES FROM (%L) TO (%L)',
                   partition_name, table_name, start_date, end_date);
                   
    -- Create indexes on partition
    EXECUTE format('CREATE INDEX %I ON %I (customer_id)',
                   partition_name || '_customer_idx', partition_name);
END;
$$ LANGUAGE plpgsql;

-- Create partitions for the year
SELECT create_monthly_partition('sales_data', '2024-01-01'::DATE + (n || ' months')::INTERVAL)
FROM generate_series(0, 11) n;
```


#### Multi-Level Partitioning

```sql
-- Sub-partitioning by region within time partitions
CREATE TABLE sales_2024_01 PARTITION OF sales_data
    FOR VALUES FROM ('2024-01-01') TO ('2024-02-01')
    PARTITION BY LIST (region);

CREATE TABLE sales_2024_01_north_america PARTITION OF sales_2024_01
    FOR VALUES IN ('US', 'CA', 'MX');

CREATE TABLE sales_2024_01_europe PARTITION OF sales_2024_01
    FOR VALUES IN ('UK', 'DE', 'FR', 'IT', 'ES');
```


### MongoDB Sharding Strategies[^10]

#### Zone-Based Sharding

```javascript
// Configure zones for geographic data isolation
sh.addShardToZone("shard0000", "NA")  // North America
sh.addShardToZone("shard0001", "EU")  // Europe
sh.addShardToZone("shard0002", "APAC") // Asia-Pacific

// Create zone ranges
sh.updateZoneKeyRange(
  "ecommerce.customers",
  { "region": "US", "customer_id": MinKey },
  { "region": "US", "customer_id": MaxKey },
  "NA"
)

sh.updateZoneKeyRange(
  "ecommerce.customers", 
  { "region": "DE", "customer_id": MinKey },
  { "region": "DE", "customer_id": MaxKey },
  "EU"
)
```


#### Hashed Sharding for Even Distribution

```javascript
// Enable sharding with hashed shard key
sh.shardCollection("logs.application_events", { "_id": "hashed" })

// For time-series data with compound key
sh.shardCollection("iot.sensor_readings", { 
  "device_id": "hashed", 
  "timestamp": 1 
})
```


### Cassandra Advanced Partitioning[^11]

#### Composite Partition Keys for Load Distribution

```cql
-- Time-series with multiple partition key components
CREATE TABLE iot_sensor_data (
    sensor_type TEXT,
    sensor_id UUID,
    time_bucket TEXT,  -- "2024-01-15-14" (hour-level)
    timestamp TIMESTAMP,
    temperature FLOAT,
    humidity FLOAT,
    battery_level FLOAT,
    PRIMARY KEY ((sensor_type, sensor_id, time_bucket), timestamp)
) WITH CLUSTERING ORDER BY (timestamp DESC);

-- Multi-tenant data with tenant isolation
CREATE TABLE tenant_user_data (
    tenant_id UUID,
    user_id UUID,
    data_category TEXT,
    created_at TIMESTAMP,
    updated_at TIMESTAMP,
    data_blob BLOB,
    PRIMARY KEY ((tenant_id, user_id), data_category, created_at)
) WITH CLUSTERING ORDER BY (data_category ASC, created_at DESC);
```


#### Token-Aware Partitioning

```cql
-- Using token function for even distribution
CREATE TABLE distributed_cache (
    cache_key TEXT,
    cache_value BLOB,
    ttl TIMESTAMP,
    PRIMARY KEY (cache_key)
);

-- Query with token for load balancing
SELECT * FROM distributed_cache 
WHERE token(cache_key) > token('start_key') 
  AND token(cache_key) <= token('end_key');
```


## Advanced Indexing Techniques

### PostgreSQL Advanced Indexing

#### Covering Indexes

```sql
-- Index that covers all columns needed for query
CREATE INDEX idx_orders_covering ON orders 
(customer_id, order_date) 
INCLUDE (status, total_amount);

-- Query uses index-only scan
SELECT customer_id, order_date, status, total_amount
FROM orders 
WHERE customer_id = 123 
  AND order_date >= '2024-01-01';
```


#### Conditional and Functional Indexes

```sql
-- Index on function result
CREATE INDEX idx_customers_email_domain 
ON customers (substring(email FROM '@(.*)$'));

-- Conditional index for specific conditions
CREATE INDEX idx_pending_orders 
ON orders (order_date, customer_id) 
WHERE status = 'pending';

-- Expression index for case-insensitive searches
CREATE INDEX idx_products_name_lower 
ON products (LOWER(name));
```


### MongoDB Advanced Indexing[^12][^5]

#### Partial Indexes for Selective Indexing

```javascript
// Index only documents that meet specific criteria
db.orders.createIndex(
  { "customer_id": 1, "order_date": -1 },
  { 
    partialFilterExpression: { 
      "status": { $in: ["processing", "shipped"] },
      "total_amount": { $gt: 100 }
    }
  }
)

// Sparse index - only for documents with the field
db.users.createIndex(
  { "premium_membership.expiry_date": 1 },
  { sparse: true }
)
```


#### Wildcard Indexes

```javascript
// Index on all fields matching a pattern
db.products.createIndex({ "attributes.$**": 1 })

// Query can use the wildcard index
db.products.find({ "attributes.color": "red" })
db.products.find({ "attributes.size": "large" })
```


#### Index Intersection

```javascript
// Create separate indexes that can be intersected
db.inventory.createIndex({ "location": 1 })
db.inventory.createIndex({ "product_type": 1 })
db.inventory.createIndex({ "quantity": 1 })

// MongoDB can intersect these indexes for complex queries
db.inventory.find({
  "location": "warehouse_a",
  "product_type": "electronics",
  "quantity": { $gt: 100 }
})
```


### Cassandra Secondary Indexes and Materialized Views

#### SASI (SSTable Attached Secondary Indexes)

```cql
-- SASI index for range and LIKE queries
CREATE CUSTOM INDEX ON products (description) 
USING 'org.apache.cassandra.index.sasi.SASIIndex' 
WITH OPTIONS = {
  'mode': 'CONTAINS',
  'analyzer_class': 'org.apache.cassandra.index.sasi.analyzer.StandardAnalyzer',
  'tokenization_enable_stemming': 'true'
};

-- Query with LIKE operation
SELECT * FROM products 
WHERE description LIKE '%laptop%';
```


#### Materialized Views for Denormalization

```cql
-- Base table
CREATE TABLE user_posts (
    user_id UUID,
    post_id UUID,
    created_at TIMESTAMP,
    title TEXT,
    content TEXT,
    tags SET<TEXT>,
    likes_count INT,
    PRIMARY KEY (user_id, created_at, post_id)
) WITH CLUSTERING ORDER BY (created_at DESC, post_id ASC);

-- Materialized view for querying by tags
CREATE MATERIALIZED VIEW posts_by_tag AS
SELECT user_id, post_id, created_at, title, tags, likes_count
FROM user_posts
WHERE tags IS NOT NULL 
  AND user_id IS NOT NULL 
  AND created_at IS NOT NULL 
  AND post_id IS NOT NULL
PRIMARY KEY (tags, created_at, user_id, post_id)
WITH CLUSTERING ORDER BY (created_at DESC, user_id ASC, post_id ASC);
```


## Production-Grade Best Practices

### High Availability and Disaster Recovery

#### PostgreSQL HA Setup

```bash
# Streaming replication configuration
# primary server postgresql.conf
wal_level = replica
max_wal_senders = 3
wal_keep_segments = 64
synchronous_standby_names = 'standby1'

# pg_hba.conf
host replication replicator 10.0.0.0/24 md5

# standby server recovery.conf
standby_mode = 'on'
primary_conninfo = 'host=primary_server port=5432 user=replicator'
trigger_file = '/tmp/postgresql.trigger'
```


#### MongoDB Replica Set Configuration

```javascript
// Initialize replica set
rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "mongo1:27017", priority: 2 },
    { _id: 1, host: "mongo2:27017", priority: 1 },
    { _id: 2, host: "mongo3:27017", priority: 1, arbiterOnly: true }
  ]
})

// Read preference for scaling reads
db.orders.find().readPref("secondaryPreferred")
```


#### Cassandra Multi-DC Setup

```cql
-- Multi-datacenter replication strategy
CREATE KEYSPACE ecommerce
WITH REPLICATION = {
  'class': 'NetworkTopologyStrategy',
  'datacenter1': 3,
  'datacenter2': 2
};

-- Local consistency for better performance
CONSISTENCY LOCAL_QUORUM;
```


### Monitoring and Performance Tuning

#### PostgreSQL Monitoring

```sql
-- Query performance monitoring
SELECT 
  query,
  calls,
  total_time,
  mean_time,
  rows,
  (total_time / calls) as avg_time_ms
FROM pg_stat_statements 
ORDER BY total_time DESC 
LIMIT 10;

-- Index usage statistics
SELECT 
  schemaname,
  tablename,
  indexname,
  idx_scan,
  idx_tup_read,
  idx_tup_fetch
FROM pg_stat_user_indexes 
ORDER BY idx_scan DESC;

-- Table bloat monitoring
SELECT 
  schemaname,
  tablename,
  n_dead_tup,
  n_live_tup,
  (n_dead_tup::float / (n_live_tup + n_dead_tup)) * 100 as bloat_percent
FROM pg_stat_user_tables 
WHERE n_live_tup > 0
ORDER BY bloat_percent DESC;
```


#### MongoDB Performance Monitoring

```javascript
// Database profiler
db.setProfilingLevel(2, { slowms: 100 })

// Index usage stats
db.orders.aggregate([
  { $indexStats: {} }
])

// Operation statistics
db.runCommand({ serverStatus: 1 }).opcounters

// Sharding balance
sh.status()
```


#### Cassandra Monitoring

```cql
-- Nodetool commands for monitoring
nodetool status
nodetool tpstats
nodetool tablestats keyspace.table
nodetool compactionstats

-- CQL monitoring queries
SELECT * FROM system.compaction_history;
SELECT * FROM system_schema.indexes;
```


### Security Best Practices

#### Database Access Control

```sql
-- PostgreSQL role-based security
CREATE ROLE app_read_only;
GRANT CONNECT ON DATABASE ecommerce TO app_read_only;
GRANT USAGE ON SCHEMA public TO app_read_only;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO app_read_only;

CREATE ROLE app_read_write;
GRANT app_read_only TO app_read_write;
GRANT INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_read_write;

-- Row-level security
CREATE POLICY customer_policy ON orders
  FOR ALL TO app_user
  USING (customer_id = current_setting('app.current_customer_id')::INTEGER);

ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
```


#### Data Encryption

```sql
-- PostgreSQL TDE (Transparent Data Encryption)
-- postgresql.conf
ssl = on
ssl_cert_file = 'server.crt'
ssl_key_file = 'server.key'
ssl_ca_file = 'ca.crt'

-- Column-level encryption
CREATE EXTENSION pgcrypto;

INSERT INTO sensitive_data (id, encrypted_ssn) 
VALUES (1, pgp_sym_encrypt('123-45-6789', 'encryption_key'));

SELECT id, pgp_sym_decrypt(encrypted_ssn, 'encryption_key') 
FROM sensitive_data;
```


### Backup and Recovery Strategies

#### PostgreSQL Backup Strategies

```bash
# Point-in-time recovery setup
# postgresql.conf
wal_level = replica
archive_mode = on
archive_command = 'test ! -f /backup/archive/%f && cp %p /backup/archive/%f'

# Full backup
pg_basebackup -D /backup/base -Ft -z -P -U postgres

# Logical backup
pg_dump -h localhost -U postgres -d ecommerce > ecommerce_backup.sql

# Continuous archiving with WAL-E
wal-e backup-push /var/lib/postgresql/data
```


#### MongoDB Backup Strategies

```bash
# Replica set backup
mongodump --host rs0/mongo1:27017,mongo2:27017,mongo3:27017 --db ecommerce

# Sharded cluster backup
mongodump --host config1:27019 --oplog
mongodump --host shard1/mongo-s1-1:27017,mongo-s1-2:27017 --oplog
mongodump --host shard2/mongo-s2-1:27017,mongo-s2-2:27017 --oplog

# Point-in-time recovery with oplog replay
mongorestore --oplogReplay --oplogLimit 1640995200:1
```


#### Cassandra Backup Strategies

```bash
# Snapshot backup
nodetool snapshot ecommerce

# Incremental backup
nodetool rebuild_index ecommerce orders
nodetool flush ecommerce orders

# Cross-datacenter replication for DR
# cassandra.yaml
endpoint_snitch: GossipingPropertyFileSnitch
```


## Cheat Sheet

### Query Performance Quick Reference

| Database | Slow Query Analysis | Index Optimization | Partitioning |
| :-- | :-- | :-- | :-- |
| **PostgreSQL** | `EXPLAIN ANALYZE` | Covering indexes, Partial indexes | Range, Hash, List |
| **MongoDB** | `db.collection.explain()` | Compound indexes (ESR rule) | Sharding by shard key |
| **Cassandra** | `TRACING ON` | Secondary indexes, Materialized views | Partition key design |
| **Redis** | `SLOWLOG` | Sorted sets, Hash indexes | Cluster mode sharding |

### Connection Limits and Scaling

| Database | Max Connections | Scaling Strategy | Pool Recommended |
| :-- | :-- | :-- | :-- |
| **PostgreSQL** | ~200-400 | Read replicas, Connection pooling | PgBouncer, PgPool |
| **MongoDB** | ~65K | Replica sets, Sharding | Built-in connection pooling |
| **Cassandra** | ~2K per node | Add nodes linearly | DataStax driver pooling |
| **Redis** | ~10K | Cluster mode, Read replicas | Redis connection pooling |

### Data Size Limits

| Database | Single Record | Collection/Table | Database |
| :-- | :-- | :-- | :-- |
| **PostgreSQL** | 1.6TB (row) | Unlimited | Unlimited |
| **MongoDB** | 16MB (document) | Unlimited | 64TB |
| **Cassandra** | 2GB (partition) | Unlimited | Unlimited |
| **Redis** | 512MB (key) | Unlimited | Limited by memory |

### Consistency Models

| Database | Default | Options | Use Case |
| :-- | :-- | :-- | :-- |
| **PostgreSQL** | ACID Strong | Serializable, Read Committed | Financial transactions |
| **MongoDB** | Eventual | Strong, Eventual, Causal | Content management |
| **Cassandra** | Eventual | ONE, QUORUM, ALL | Analytics, IoT |
| **Redis** | Strong | Strong, Eventual | Caching, sessions |

This comprehensive guide provides production-ready knowledge for designing, implementing, and maintaining database systems in corporate environments. Each database type has its strengths, and the choice depends on specific requirements like consistency needs, scalability patterns, query complexity, and operational constraints.

<div style="text-align: center">⁂</div>

[^1]: https://aws.amazon.com/nosql/in-memory/

[^2]: https://en.wikipedia.org/wiki/In-memory_database

[^3]: https://airbyte.com/data-engineering-resources/sql-vs-nosql

[^4]: https://www.geeksforgeeks.org/difference-between-sql-and-nosql/

[^5]: https://www.mongodb.com/blog/post/performance-best-practices-indexing

[^6]: https://opensource.com/article/20/5/apache-cassandra

[^7]: https://stackoverflow.com/questions/42681606/cassandra-partition-and-cluster-keys-to-store-inverted-index

[^8]: https://dba.stackexchange.com/questions/332842/how-to-stablish-a-partitions-indexes-stategy-in-postgres-for-100s-of-milliion

[^9]: https://dev.to/msdousti/postgresql-partitioning-with-desired-index-names-1gcd

[^10]: https://www.mongodb.com/community/forums/t/vector-search-index-partitioning/303836

[^11]: https://docs.datastax.com/en/cql-oss/3.3/cql/cql_using/useCompositePartitionKeyConcept.html

[^12]: https://www.geopits.com/blog/mongodb-indexing-strategies.html

[^13]: https://www.mongodb.com/resources/basics/databases/in-memory-database

[^14]: https://aerospike.com/blog/what-is-an-in-memory-database/

[^15]: https://www.couchbase.com/resources/concepts/in-memory-database/

[^16]: https://www.wallarm.com/cloud-native-products-101/mongodb-vs-cassandra-nosql-databases

[^17]: https://www.gridgain.com/resources/glossary/in-memory-computing-platform/in-memory-database

[^18]: https://www.youtube.com/watch?v=qLBXAU51s4U

[^19]: https://www.sap.com/resources/in-memory-database

[^20]: https://www.geeksforgeeks.org/sql/difference-between-sql-and-nosql/

