proyek utama Anda adalah mengevaluasi kinerja bisnis Kimia Farma dari tahun 2020 hingga 2023

berikut adalah query yang digunakan

CREATE TABLE coba_analisa (
    transaction_id VARCHAR PRIMARY KEY,
    date DATE,
    branch_id VARCHAR,
    branch_name VARCHAR(255),
    kota VARCHAR(255),
    provinsi VARCHAR(255),
    rating_cabang DECIMAL(3, 2),
    customer_name VARCHAR(255),
    product_id VARCHAR,
    product_name VARCHAR(255),
    actual_price DECIMAL(10, 2),
    discount_percentage DECIMAL(5, 2),
    persentase_gross_laba DECIMAL(5, 2),
    nett_sales DECIMAL(10, 2),
    nett_profit DECIMAL(10, 2),
    rating_transaksi DECIMAL(3, 2)
);


TRUNCATE TABLE analisa;

INSERT INTO analisa (
    transaction_id,
    date,
    branch_id,
    branch_name,
    kota,
    provinsi,
    rating_cabang,
    customer_name,
    product_id,
    product_name,
    actual_price,
    discount_percentage,
    persentase_gross_laba,
    nett_sales,
    nett_profit,
    rating_transaksi
)
SELECT 
    ft.transaction_id,
    ft.date,
    kc.branch_id,
    kc.branch_name,
    kc.kota,
    kc.provinsi,
    kc.rating,
    ft.customer_name,
    p.product_id,
    p.product_name,
    p.price,
    ft.discount_percentage,
    CASE
        WHEN p.price <= 50000 THEN 10
        WHEN p.price > 50000 AND p.price <= 100000 THEN 15
        WHEN p.price > 100000 AND p.price <= 300000 THEN 20
        WHEN p.price > 300000 AND p.price <= 500000 THEN 25
        ELSE 30
    END AS persentase_gross_laba,
    (p.price * (1 - ft.discount_percentage / 100)) AS nett_sales,
    (p.price * (1 - ft.discount_percentage / 100)) * 
    CASE
        WHEN p.price <= 50000 THEN 0.10
        WHEN p.price > 50000 AND p.price <= 100000 THEN 0.15
        WHEN p.price > 100000 AND p.price <= 300000 THEN 0.20
        WHEN p.price > 300000 AND p.price <= 500000 THEN 0.25
        ELSE 0.30
    END AS nett_profit,
    ft.rating
FROM 
    kf_final_transaction ft
JOIN 
    kf_kantor_cabang kc ON ft.branch_id = kc.branch_id
JOIN 
    kf_product p ON ft.product_id = p.product_id;
