INSERT INTO fulfillment (id, customer_id, reward_catalog_item_id, qty, status, creation_date)
SELECT 
    (SELECT MAX(id) FROM fulfillment) + ROW_NUMBER() OVER(),
    FLOOR(RAND() * 3) + 1, 
    FLOOR(RAND() * 40) + 1, 
    FLOOR(RAND() * 20) + 1, 
    'Success', 
    DATE_ADD('2022-01-01', INTERVAL FLOOR(RAND() * 1100) DAY)
FROM information_schema.tables
LIMIT 90;