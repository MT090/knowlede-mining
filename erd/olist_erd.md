# Olist ERD – Five Tables (Crow's Foot Notation)

```mermaid
erDiagram

    customers {
        string customer_id          PK
        string customer_unique_id
        string customer_zip_code_prefix
        string customer_city
        string customer_state
    }

    orders {
        string order_id             PK
        string customer_id          FK
        string order_status
        timestamp order_purchase_timestamp
        timestamp order_approved_at
        timestamp order_delivered_carrier_date
        timestamp order_delivered_customer_date
        timestamp order_estimated_delivery_date
    }

    order_items {
        string order_id             PK,FK
        int    order_item_id        PK
        string product_id           FK
        string seller_id            FK
        timestamp shipping_limit_date
        float  price
        float  freight_value
    }

    products {
        string product_id           PK
        string product_category_name
        int    product_name_lenght
        int    product_description_lenght
        int    product_photos_qty
        float  product_weight_g
        float  product_length_cm
        float  product_height_cm
        float  product_width_cm
    }

    sellers {
        string seller_id            PK
        string seller_zip_code_prefix
        string seller_city
        string seller_state
    }

    customers ||--o{ orders        : "places"
    orders    ||--o{ order_items   : "contains"
    products  ||--o{ order_items   : "listed in"
    sellers   ||--o{ order_items   : "fulfils"
```

## Cardinality notes (derived from data)

| Relationship | Min children | Max children | Notation |
|---|---|---|---|
| customers → orders | 1 | 1 | `\|\|--o{` (every customer_id appears in exactly one order row, but a person may re-order under a new customer_id) |
| orders → order_items | 0 | 21 | `\|\|--o{` (775 orders have zero items) |
| products → order_items | 0 | 527 | `\|\|--o{` (some products were never sold) |
| sellers → order_items | 0 | many | `\|\|--o{` (some sellers have no items in the data) |

> The circle (o) on the "many" end expresses **zero-or-many**, which the data supports:
> orders without items and products/sellers with no associated order_items both exist.
