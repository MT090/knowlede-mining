# Data Quality Notes – Assignment 1

## a. Two customer identifier columns

The customers table has both `customer_id` and `customer_unique_id` and at first I thought
they were the same thing but they're not. `customer_id` is basically tied to a single order,
so if the same person orders twice they get two different `customer_id` values. That makes it
useless for counting actual customers. `customer_unique_id` stays the same for the same person
across orders, so that's the one you'd use with `COUNT(DISTINCT ...)` to get the real number
of customers. I confirmed this by checking that `customer_id` has as many distinct values as
there are rows, while `customer_unique_id` has fewer.

## b. Junction table

`order_items` is the junction table. The problem is that orders and products have a
many-to-many relationship — one order can have multiple products and one product can show up
in many orders. You can't store that directly so `order_items` sits in the middle and breaks
it into two one-to-many relationships. It also connects sellers at the same time since each
line item has a seller_id, so it's actually resolving three relationships at once which I
thought was interesting.

## c. Strict vs. circle-justified relationships

One relationship that's stricter than the diagram shows is `customers → orders`. I drew it
as zero-or-many on the orders side but the data actually shows every customer row has exactly
one matching order, never zero. So the circle isn't really needed there, the data is tighter
than I drew it.

The circle that is justified is `orders → order_items`. I ran the anti-join and found 775
orders that have no items at all, mostly with status "unavailable" or "canceled". So an order
genuinely can exist with zero items and the circle belongs there.
