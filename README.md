# Medallion E-Commerce Pipeline

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![Databricks](https://img.shields.io/badge/Databricks-Community-FF3621?logo=databricks&logoColor=white)
![Delta Lake](https://img.shields.io/badge/Delta_Lake-3.0-003366?logo=delta&logoColor=white)
![Unity Catalog](https://img.shields.io/badge/Unity_Catalog-Enabled-green)
![PySpark](https://img.shields.io/badge/PySpark-3.5-E25A1C?logo=apachespark&logoColor=white)
![Status](https://img.shields.io/badge/Status-Concluído-brightgreen)

Pipeline de dados completo com arquitetura **Medallion (Raw → Silver → Gold)** construído no Databricks Community Edition, usando dados reais de e-commerce brasileiro (Olist/Kaggle).

---

## Objetivo

Demonstrar na prática a implementação de uma arquitetura Medallion moderna com:
- Ingestão e governança de dados com **Unity Catalog**
- Transformações incrementais com **PySpark** e **Delta Lake**
- Camada analítica com agregações de negócio reais
- Rastreabilidade completa via **Data Lineage**

---

## Arquitetura

```mermaid
flowchart LR
    A[ Olist CSVs\n9 arquivos] --> B[ RAW\n9 tabelas Delta\nSchema original]
    B --> C[SILVER\n5 tabelas Delta\nLimpeza e conformidade]
    C --> D[GOLD\n3 tabelas Delta\nAgregações de negócio]
    D --> E[Analytics\nBI / Dashboards]

    subgraph Unity Catalog
        B
        C
        D
    end
```

---

## Stack

| Tecnologia | Uso |
|---|---|
| Databricks Community | Ambiente de execução |
| PySpark | Transformações distribuídas |
| Delta Lake | Formato de armazenamento |
| Unity Catalog | Governança e lineage |
| Python 3.10+ | Linguagem principal |

---

## Estrutura do projeto

```
medallion-ecommerce-pipeline/
├── notebooks/
│   ├── 00_setup.ipynb               # Configuração inicial
│   ├── 01_raw_ingestion.ipynb       # Camada Raw
│   ├── 02_silver_transform.ipynb    # Camada Silver
│   └── 03_gold_aggregations.ipynb   # Camada Gold
├── src/
│   └── utils.py                     # Funções utilitárias
├── tests/
│   └── test_transformations.py      # Testes unitários
├── docs/
│   └── architecture.png             # Diagrama de arquitetura
└── README.md
```

---

## Camadas do pipeline

### Raw — Ingestão
- Leitura dos 9 CSVs do dataset Olist via PySpark
- Salvamento como Delta Tables sem transformações
- Adição de colunas de rastreabilidade: `_ingested_at`, `_source_file`
- Registro no Unity Catalog com comentários de coluna

**Tabelas:** `olist_orders_dataset`, `olist_customers_dataset`, `olist_order_items_dataset`, `olist_order_payments_dataset`, `olist_order_reviews_dataset`, `olist_products_dataset`, `olist_sellers_dataset`, `olist_geolocation_dataset`, `product_category_name_translation`

### Silver — Limpeza e Conformidade
- Remoção de duplicatas e nulos
- Conversão de datas para `TimestampType`
- Conversão de valores monetários para `DoubleType`
- Uso de `try_cast` para dados sujos
- Rastreabilidade completa por registro

**Tabelas:** `olist_orders`, `olist_customers`, `olist_payments`, `olist_reviews`, `olist_sellers`

### Gold — Agregações de Negócio
- **`orders_summary`** — Receita, ticket médio e NPS por estado e mês
- **`customer_ltv`** — LTV e ticket médio por estado
- **`delivery_performance`** — Prazo médio, atrasos e NPS por estado

---

## Governança

- 3 schemas isolados no Unity Catalog: `raw`, `silver`, `gold`
- `GRANT SELECT ON SCHEMA gold TO account users`
- Data Lineage automático rastreando origem de cada coluna
- Comentários em todas as tabelas e colunas principais

---

## Como rodar

### Pré-requisitos
- Conta no [Databricks Community Edition](https://community.cloud.databricks.com)
- Dataset [Brazilian E-Commerce (Olist)](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) no Kaggle

### Passo a passo

1. Faça o upload dos 9 CSVs em `Volumes/workspace/raw/landing/`
2. Execute os notebooks na ordem:
   - `01_raw_ingestion`
   - `02_silver_transform`
   - `03_gold_aggregations`

---

## Resultados

| Camada | Tabelas | Total de linhas |
|---|---|---|
| Raw | 9 | ~1.5M |
| Silver | 5 | ~500k |
| Gold | 3 | ~600 |

---

## Autor

**Vinicius Trindade da Silva**
Analytics Engineer | Data Engineering

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Vinicius_Trindade-0077B5?logo=linkedin)](https://linkedin.com/in/viniitrindadee)
[![GitHub](https://img.shields.io/badge/GitHub-ViniiTrindadee-181717?logo=github)](https://github.com/ViniiTrindadee)
