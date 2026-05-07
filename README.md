# SQL Database Relationships example – README

This represents a simple database structure for an online store.

This small database contains:

* sellers
* customers
* products
* orders
* order_items

---

# Table Relationships

```text
customers
    ↓
orders
    ↓
order_items
    ↓
products
    ↓
sellers
```

---

# Explanation of Each Table

## 1. sellers

The `sellers` table stores information about sellers.

### Columns

| Column        | Type    | Description       |
| ------------- | ------- | ----------------- |
| seller_id     | INT     | Unique seller ID  |
| seller_f_name | VARCHAR | Seller first name |
| seller_l_name | VARCHAR | Seller last name  |
| email         | VARCHAR | Seller email      |

### SQL

```sql
CREATE TABLE sellers (
    seller_id INT PRIMARY KEY AUTO_INCREMENT,
    seller_f_name VARCHAR(50),
    seller_l_name VARCHAR(50),
    email VARCHAR(50)
);
```

---

## 2. customers

The `customers` table stores information about customers.

### Columns

| Column          | Type    | Description         |
| --------------- | ------- | ------------------- |
| customer_id     | INT     | Unique customer ID  |
| customer_f_name | VARCHAR | Customer first name |
| customer_l_name | VARCHAR | Customer last name  |
| email           | VARCHAR | Customer email      |

### SQL

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_f_name VARCHAR(50),
    customer_l_name VARCHAR(50),
    email VARCHAR(50)
);
```

---

## 3. products

The `products` table stores all store products.

Each product belongs to a seller.

### Relationship

```text
products.seller_id → sellers.seller_id
```

### Columns

| Column       | Type    | Description         |
| ------------ | ------- | ------------------- |
| product_id   | INT     | Product ID          |
| seller_id    | INT     | Reference to seller |
| product_name | VARCHAR | Product name        |
| price        | INT     | Product price       |
| rating       | INT     | Product rating      |

### SQL

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    seller_id INT,
    product_name VARCHAR(100),
    price INT,
    rating INT,

    FOREIGN KEY (seller_id)
    REFERENCES sellers(seller_id)
);
```

---

## 4. orders

The `orders` table represents customer orders.

One customer can have multiple orders.

### Relationship

```text
orders.customer_id → customers.customer_id
```

### Columns

| Column      | Type      | Description                   |
| ----------- | --------- | ----------------------------- |
| order_id    | INT       | Order ID                      |
| customer_id | INT       | Customer who placed the order |
| created_at  | TIMESTAMP | Order creation date           |

### SQL

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (customer_id)
    REFERENCES customers(customer_id)
);
```

---

## 5. order_items

This is the main relationship table.

It connects:

* orders
* products

Why?

Because:

* one order can contain multiple products
* the same product can appear in multiple orders

This creates a many-to-many relationship.

### Relationships

```text
order_items.order_id → orders.order_id
order_items.product_id → products.product_id
```

### Columns

| Column        | Type | Description      |
| ------------- | ---- | ---------------- |
| order_item_id | INT  | Unique row ID    |
| order_id      | INT  | Related order    |
| product_id    | INT  | Related product  |
| quantity      | INT  | Product quantity |

### SQL

```sql
CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT,
    product_id INT,
    quantity INT,

    FOREIGN KEY (order_id)
    REFERENCES orders(order_id),

    FOREIGN KEY (product_id)
    REFERENCES products(product_id)
);
```

--

A customer places an order:

```text
Customer → Order → Order Items → Products → Seller
```

# Example JOIN Query

This query displays:

* customer name
* ordered product
* price
* seller

```sql
SELECT
    orders.order_id,
    customers.customer_f_name,
    products.product_name,
    products.price,
    sellers.seller_f_name
FROM orders
JOIN customers
    ON orders.customer_id = customers.customer_id
JOIN order_items
    ON orders.order_id = order_items.order_id
JOIN products
    ON order_items.product_id = products.product_id
JOIN sellers
    ON products.seller_id = sellers.seller_id;
```

---

# Main Concepts Used

| Concept      | Explanation                          |
| ------------ | ------------------------------------ |
| PRIMARY KEY  | Unique identifier for each row       |
| FOREIGN KEY  | Creates relationships between tables |
| ONE TO MANY  | One customer can have many orders    |
| MANY TO MANY | Orders can contain many products     |
| JOIN         | Combines data from multiple tables   |

---

# Final Notes

This structure is scalable and follows common relational database design practices used in real-world ecommerce applications.

It allows:

* multiple customers
* multiple sellers
* multiple products
* multiple products per order
* flexible SQL queries using JOINs

* Thank you.
