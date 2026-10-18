# goit-rdb-hw-05: CTE-pipeline ознак для ML на Olist

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/goit-homework/goit-rdb-hw-05/blob/main/hw5_olist_feature_pipeline.ipynb)

Домашнє завдання до Теми 5 курсу «Реляційні бази даних для Data Science» (GoIT Neoversity).

## Дані

[Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), версія 2, ліцензія CC BY-NC-SA 4.0. Ноутбук сам завантажує п'ять CSV (customers, orders, order_items, products, sellers) через `kagglehub` без облікових даних Kaggle і звіряє SHA-256 кожного файлу. Ті самі файли лежать у `data/olist/`.

## Шари даних

- raw `olist_*_raw`: CSV, прочитані як текст і завантажені через `to_sql` без змін.
- typed `olist_*`: схема з умови з PK, FK і `CHECK`, перенос із raw через `INSERT ... SELECT` з приведенням типів.

## CTE-stages

В Olist кожен `customer_id` має рівно одне замовлення, тому ознаки рахуються для покупця `customer_unique_id`.

| Stage | CTE | Що робить |
|---|---|---|
| 0 | `reference_date` | дата відліку: наступний день після останнього замовлення |
| 1 | `stage1_orders_total` | рядок на замовлення: покупець, статус, сума позицій із доставкою |
| 2 | `stage2_rfm` | `recency_days`, `frequency`, `monetary` і дата першого замовлення |
| 3 | `stage3_cohort` | `cohort_quarter`, квартал першого замовлення |
| 4 | `stage4_rolling_aov` | 30-денний ковзний середній чек на момент останнього замовлення |
| 5 | `stage5_cancel_rate` | частка скасованих замовлень |
| 6 | `stage6_category_revenue` | виручка покупця за категоріями товарів |
| 7 | `stage7_preferred_category` | категорія з найбільшою виручкою через `ROW_NUMBER()` |

Фінальний `SELECT` дає рядок на кожен `customer_id` з ознаками його покупця. З нього побудовано materialized view `mv_olist_customer_features` з `UNIQUE INDEX` на `customer_id`.

## Запуск

Ноутбук відкривається кнопкою Colab, далі Runtime > Run all. Локально потрібен Python 3.12:

```bash
pip install pgserver psycopg2-binary "sqlalchemy>=2.0,<2.1" pandas kagglehub jupyter
```

```bash
jupyter nbconvert --to notebook --execute --inplace hw5_olist_feature_pipeline.ipynb
```
