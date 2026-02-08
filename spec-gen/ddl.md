Here is a comprehensive PostgreSQL DDL (Data Definition Language) script designed to mimic a **Customer Information Facility (CIF)**.

This schema is designed to be **modern and demo-ready**. It includes:
1.  **UUIDs** for secure primary keys.
2.  **JSONB** columns to demonstrate flexibility (e.g., storing varied KYC documents or product features).
3.  **Enums** for strict status control.
4.  **Audit timestamps** (`created_at`, `updated_at`) which are essential in banking.

### PostgreSQL DDL Script

```sql
-- 1. Enable UUID extension for generating unique IDs
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- 2. Define ENUM types for standardizing statuses
CREATE TYPE kyc_status_type AS ENUM ('PENDING', 'VERIFIED', 'REJECTED', 'FLAGGED');
CREATE TYPE account_status_type AS ENUM ('ACTIVE', 'DORMANT', 'FROZEN', 'CLOSED');
CREATE TYPE product_category_type AS ENUM ('SAVINGS', 'CHECKING', 'CREDIT_CARD', 'LOAN', 'INVESTMENT');
CREATE TYPE transaction_type_type AS ENUM ('DEPOSIT', 'WITHDRAWAL', 'TRANSFER_IN', 'TRANSFER_OUT', 'PAYMENT', 'INTEREST');

-- 3. CUSTOMERS Table (The "Person" entity)
-- Stores the core identity information (CIF).
CREATE TABLE customers (
    customer_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    cif_number VARCHAR(20) UNIQUE NOT NULL, -- Human-readable ID (e.g., 8839201)
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    date_of_birth DATE NOT NULL,
    email VARCHAR(150) UNIQUE NOT NULL,
    phone_number VARCHAR(20),
    
    -- Address information (Stored flat here for simplicity, or could be a separate table)
    address_line1 VARCHAR(255),
    city VARCHAR(100),
    state VARCHAR(100),
    postal_code VARCHAR(20),
    country_code CHAR(2), -- ISO 3166-1 alpha-2
    
    -- Banking specifics
    kyc_status kyc_status_type DEFAULT 'PENDING',
    kyc_documents JSONB DEFAULT '{}', -- Flexible storage for doc references (Passport, ID numbers)
    risk_rating VARCHAR(10) DEFAULT 'LOW', -- LOW, MED, HIGH
    
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- 4. PRODUCTS CATALOG Table
-- Defines what the bank sells.
CREATE TABLE products (
    product_code VARCHAR(50) PRIMARY KEY, -- e.g., 'SAV-GOLD', 'LN-MORTGAGE'
    name VARCHAR(100) NOT NULL,
    category product_category_type NOT NULL,
    description TEXT,
    interest_rate NUMERIC(5, 4), -- e.g., 0.0500 for 5%
    currency_code CHAR(3) DEFAULT 'USD',
    is_active BOOLEAN DEFAULT TRUE
);

-- 5. ACCOUNTS Table (The "Financial Product" instance)
-- Links a Customer to a Product.
CREATE TABLE accounts (
    account_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    customer_id UUID NOT NULL REFERENCES customers(customer_id) ON DELETE CASCADE,
    product_code VARCHAR(50) NOT NULL REFERENCES products(product_code),
    
    account_number VARCHAR(30) UNIQUE NOT NULL,
    iban VARCHAR(34),
    
    -- Financials
    balance NUMERIC(15, 2) DEFAULT 0.00 CHECK (balance >= 0 OR overdraft_limit > 0),
    currency_code CHAR(3) NOT NULL DEFAULT 'USD',
    overdraft_limit NUMERIC(15, 2) DEFAULT 0.00,
    
    status account_status_type DEFAULT 'ACTIVE',
    opened_at TIMESTAMPTZ DEFAULT NOW(),
    closed_at TIMESTAMPTZ,
    
    -- Flexible metadata (e.g., specific interest overrides, card preferences)
    configurations JSONB DEFAULT '{}'
);

-- 6. TRANSACTIONS Table
-- Essential to show account activity in a demo.
CREATE TABLE transactions (
    transaction_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    account_id UUID NOT NULL REFERENCES accounts(account_id),
    reference_code VARCHAR(50), -- External ref ID
    
    transaction_type transaction_type_type NOT NULL,
    amount NUMERIC(15, 2) NOT NULL,
    currency_code CHAR(3) NOT NULL DEFAULT 'USD',
    
    description TEXT,
    transaction_date TIMESTAMPTZ DEFAULT NOW(),
    
    -- Running balance snapshot after this transaction (useful for faster querying)
    balance_after NUMERIC(15, 2)
);

-- 7. Indexes for Performance
-- Creating indexes on columns frequently used for searching in a generic app.
CREATE INDEX idx_customers_cif ON customers(cif_number);
CREATE INDEX idx_customers_email ON customers(email);
CREATE INDEX idx_accounts_customer ON accounts(customer_id);
CREATE INDEX idx_transactions_account ON transactions(account_id);
CREATE INDEX idx_transactions_date ON transactions(transaction_date);

-- 8. Auto-update 'updated_at' trigger function
CREATE OR REPLACE FUNCTION update_modified_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ language 'plpgsql';

CREATE TRIGGER update_customer_modtime
    BEFORE UPDATE ON customers
    FOR EACH ROW
    EXECUTE FUNCTION update_modified_column();
```

### How to use this for your Demo

1.  **Product Catalog First:** Populate the `products` table first (e.g., "Gold Savings", "Student Checking").
2.  **Customer Onboarding:** Create a row in `customers`. This generates a `customer_id`.
3.  **Account Creation:** Create a row in `accounts` using that `customer_id` and a `product_code`.
4.  **JSONB Power:** If you want to show off modern tech, use the `kyc_documents` column. You can store data like `{"document_type": "passport", "id_number": "A1234567"}` without needing to change the database schema.

### Sample Seed Data (Optional)

Run this immediately after the DDL to have data to look at:

```sql
-- Insert Products
INSERT INTO products (product_code, name, category, interest_rate) VALUES 
('CHK-STD', 'Standard Checking', 'CHECKING', 0.00),
('SAV-HYS', 'High Yield Savings', 'SAVINGS', 0.045);

-- Insert a Customer
INSERT INTO customers (cif_number, first_name, last_name, email, date_of_birth, kyc_status) 
VALUES ('CIF-1001', 'John', 'Doe', 'john.doe@example.com', '1985-05-20', 'VERIFIED');

-- Insert Account for that Customer
INSERT INTO accounts (customer_id, product_code, account_number, balance, currency_code)
SELECT customer_id, 'CHK-STD', '100000001', 5000.00, 'USD'
FROM customers WHERE cif_number = 'CIF-1001';
```