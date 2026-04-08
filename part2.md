Company
companies (
  id BIGINT PRIMARY KEY,
  name VARCHAR(255),
  created_at TIMESTAMP
)

Warehouse
warehouses (
  id BIGINT PRIMARY KEY,
  company_id BIGINT,
  name VARCHAR(255),
  location VARCHAR(255),
  FOREIGN KEY (company_id) REFERENCES companies(id)
)


Product
products (
  id BIGINT PRIMARY KEY,
  name VARCHAR(255),
  sku VARCHAR(100) UNIQUE,
  price DECIMAL(10,2),
  low_stock_threshold INT,
  created_at TIMESTAMP
);


Inventory
inventory (
  id BIGINT PRIMARY KEY,
  product_id BIGINT,
  warehouse_id BIGINT,
  quantity INT,
  updated_at TIMESTAMP,
  FOREIGN KEY (product_id) REFERENCES products(id),
  FOREIGN KEY (warehouse_id) REFERENCES warehouses(id)
);


Supplier
suppliers (
  id BIGINT PRIMARY KEY,
  name VARCHAR(255),
  contact_email VARCHAR(255)
);

Product-Supplier Mapping
product_suppliers (
  product_id BIGINT,
  supplier_id BIGINT,
  PRIMARY KEY (product_id, supplier_id)
);

Inventory Logs
inventory_logs (
  id BIGINT PRIMARY KEY,
  product_id BIGINT,
  warehouse_id BIGINT,
  change_quantity INT,
  created_at TIMESTAMP
);
 Used to track stock changes over time

Relationships
One company -multiple warehouses
One product - multiple warehouses (via inventory)
Products - Suppliers (many-to-many)
Inventory logs track stock changes


Missing Requirements
What defines “recent sales”? (e.g., last 7 days / 30 days?)
Can a product have multiple suppliers or only one primary supplier?
Where is the low stock threshold stored (product level or warehouse level)?
Are bundles allowed to contain other bundles?
Do we track sales data separately?


4. Design Decisions
Separate inventory table for scalability
Unique SKU constraint for identification
Inventory logs for tracking and auditing
Many-to-many mapping for flexible supplier relationships
