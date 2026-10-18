# goit-rdb-hw-05: CTE-pipeline ознак для ML на Olist

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/goit-homework/goit-rdb-hw-05/blob/main/hw5_olist_feature_pipeline.ipynb)

Домашнє завдання до Теми 5 курсу «Реляційні бази даних для Data Science» (GoIT Neoversity).

## Дані

[Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), версія 2, ліцензія CC BY-NC-SA 4.0. Ноутбук сам завантажує п'ять CSV (customers, orders, order_items, products, sellers) через `kagglehub` без облікових даних Kaggle і звіряє SHA-256 кожного файлу, тому в репозиторії даних немає.

## Шари даних

- raw `olist_*_raw`: CSV, прочитані як текст і завантажені через `to_sql` без змін.
- typed `olist_*`: схема з умови з PK, FK і `CHECK`, перенос із raw через `INSERT ... SELECT` з приведенням типів.

В Olist кожен `customer_id` має рівно одне замовлення, тому ознаки рахуються для покупця `customer_unique_id`. Materialized view `mv_olist_customer_features` має рядок на кожен `customer_id` з ознаками його покупця.

## Запуск

Ноутбук відкривається кнопкою Colab, далі Runtime > Run all. Локально потрібен Python 3.12:

```bash
pip install pgserver psycopg2-binary "sqlalchemy>=2.0,<2.1" pandas kagglehub jupyter
```

```bash
jupyter nbconvert --to notebook --execute --inplace hw5_olist_feature_pipeline.ipynb
```
