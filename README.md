# goit-rdb-hw-05

Домашнє завдання до Теми 5 курсу Relational Databases for Data Science: feature engineering на Olist через CTE-pipeline, recursive CTE, Materialized View і `EXPLAIN (ANALYZE, BUFFERS)`.

Автор: Mykyta Oliinyk

## Дані

Датасет: Brazilian E-Commerce Public Dataset by Olist.
Офіційне джерело: https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

Notebook завантажує CSV через KaggleHub (варіант A з умови), для цього датасету облікові дані Kaggle не знадобилися. Курсовий архів і stub-schema fallback не використовуються. Файли даних у репозиторій не додаються.

Вибірка не формується: беруться всі рядки п’яти CSV. Вони завантажуються в raw-таблиці, а потім через `INSERT ... SELECT` у typed-схему з PK, FK і CHECK.

| Таблиця | Рядків |
|---|---:|
| `olist_customers` | 99 441 |
| `olist_orders` | 99 441 |
| `olist_order_items` | 112 650 |
| `olist_products` | 32 951 |
| `olist_sellers` | 3 095 |

`hw5_referrals` синтетична: ланцюжок із 9 ребер між першими 10 клієнтами. `mv_olist_customer_features` містить 99 441 рядок, по одному на `customer_id`.

## Що варто знати про результат

В Olist `customer_id` видається на кожне замовлення, тому ознаки рахуються на рівні `customer_unique_id` (96 096 людей), а результат лишається з одним рядком на `customer_id`. У людини з кількома `customer_id` ці рядки мають однакові обчислені ознаки, тому для сум у MV потрібен один рядок на `customer_unique_id`. Решта відхилень від шаблонів умови перелічена на початку notebook.

## Запуск

Google Colab: відкрити `hw_t5.ipynb` і виконати Runtime > Run all. Якщо KaggleHub попросить доступ, налаштуйте Kaggle credentials у Colab. У Colab потрібна версія середовища 2026.07 (Python 3.12): Runtime → Change runtime type → Runtime version, бо `pgserver` не має збірок для Python 3.13.

Локально потрібен Python 3.12, бо для новіших версій може не бути wheel `pgserver`:

```bash
python3.12 -m venv .venv
source .venv/bin/activate
pip install pgserver psycopg2-binary "psycopg[binary]" sqlalchemy pandas kagglehub jupyter nbconvert
jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=1800 hw_t5.ipynb
```

PostgreSQL запускається через `pgserver` у `/tmp/hw5_pg`, окреме встановлення не потрібне. Повторний запуск безпечний: об’єкти створюються через `DROP ... IF EXISTS`. Час у `EXPLAIN ANALYZE` змінюється між запусками, числа в Reflection відповідають збереженим output.

## Структура

```
goit-rdb-hw-05/
├── hw_t5.ipynb   notebook з output
├── README.md
└── .gitignore
```
