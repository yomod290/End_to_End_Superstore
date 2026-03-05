[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?logo=linkedin)](https://www.linkedin.com/in/yonathanmontenegro/)
[![GitHub](https://img.shields.io/badge/GitHub-Repo-181717?logo=github&logoColor=white)](https://github.com/yomod290/End_to_End_Superstore)
[![Notion Project](https://img.shields.io/badge/Notion-Project-black?logo=notion&logoColor=white)](https://yonathan-montenegro.notion.site/End-to-End-Superstore-Lakehouse-en-Azure-Unit-Catalog-309c42650553800aa763d0de1c3f650a)
[![Notion Portafolio](https://img.shields.io/badge/Notion-Portafolio-black?logo=notion&logoColor=white)](https://yonathan-montenegro.notion.site/portafolio-data-engineer)
[![Azure](https://img.shields.io/badge/Azure-Cloud-0078D4?logo=microsoftazure&logoColor=white)](https://azure.microsoft.com/)
[![Databricks](https://img.shields.io/badge/Databricks-Lakehouse-EF3D2C?logo=databricks&logoColor=white)](https://www.databricks.com/)
[![PySpark](https://img.shields.io/badge/PySpark-Data%20Processing-E25A1C?logo=apachespark&logoColor=white)](https://spark.apache.org/docs/latest/api/python/)


# 🚀 Proyecto End-to-End Lakehouse Data Engineering (Azure)

<img width="1298" height="722" alt="image" src="https://github.com/user-attachments/assets/e46829c5-217d-4f6d-b14d-aad10ac79b82" />


Este proyecto implementa una **arquitectura moderna de Data Engineering basada en Lakehouse en Azure**, utilizando tecnologías ampliamente usadas en entornos empresariales.

La solución sigue el patrón **Medallion Architecture (Bronze → Silver → Gold)** usando:

- **Azure Data Lake Storage Gen2**
- **Azure Databricks**
- **Delta Lake**
- **Unity Catalog**
- **Azure Synapse Serverless**
- **Power BI**

El objetivo es construir un **pipeline completo de analítica de datos**, desde la ingestión hasta la visualización de dashboards.

---

# 🏗️ Arquitectura del Proyecto

```
SQL Server (Dataset origen)
        │
        ▼
Azure Data Lake Storage Gen2
        │
        ├── Bronze Layer (Datos crudos)
        │
        ▼
Azure Databricks (PySpark + Delta Lake)
        │
        ├── Silver Layer (Datos limpios)
        │
        ▼
Gold Layer (Datos agregados de negocio)
        │
        ▼
Azure Synapse Serverless SQL
        │
        ▼
Power BI
(Dashboards analíticos)
```

Esta arquitectura permite separar claramente las etapas del procesamiento de datos y habilitar analítica escalable sobre el Data Lake.

---

# ⚙️ Tecnologías Utilizadas

| Tecnología | Uso |
|---|---|
| SQL Server | Fuente de datos |
| Azure Data Lake Storage Gen2 | Almacenamiento del Lakehouse |
| Azure Databricks | Procesamiento de datos |
| Delta Lake | Formato transaccional para Data Lake |
| Unity Catalog | Gobernanza y control de acceso |
| Azure Synapse Serverless | Consultas SQL sobre Data Lake |
| Power BI | Visualización de datos |
| Azure Key Vault | Gestión de secretos |
| GitHub | Control de versiones |

---

# 🧱 Medallion Architecture

El proyecto sigue el patrón **Medallion Architecture**, que organiza los datos en diferentes capas según su nivel de procesamiento.

```
Raw Data → Clean Data → Business Data
```

Capas:

- **Bronze** → Datos crudos ingeridos desde la fuente
- **Silver** → Datos limpios y estandarizados
- **Gold** → Datos agregados para análisis de negocio

Beneficios de este enfoque:

- Mejora la calidad de los datos
- Facilita el mantenimiento
- Permite reutilización de datasets
- Mejora el rendimiento de consultas

---

# 🥉 Bronze Layer — Datos Crudos

La capa **Bronze** contiene los datos en su forma más cercana al sistema origen.

Características:

- Datos sin transformar
- Datos almacenados en **Delta Lake**
- Mantiene trazabilidad completa
- Permite auditoría de datos

Ejemplo de estructura:

```
bronze/orders
bronze/customers
bronze/products
```

Los datos se almacenan en **Azure Data Lake Storage Gen2**.

---

# 🥈 Silver Layer — Limpieza y Transformación

La capa **Silver** realiza la limpieza y estandarización de los datos usando **Azure Databricks con PySpark**.

Transformaciones realizadas:

- Conversión de tipos de datos
- Conversión de fechas
- Eliminación de duplicados
- Validación de datos nulos
- Normalización de columnas

Ejemplo de transformaciones:

- Conversión de fechas `MM/dd/yyyy`
- Conversión de métricas numéricas
- Limpieza de registros inválidos

También se agrega metadata técnica:

```
_silver_ts
```

Esta columna indica el momento en que el registro fue procesado.

---

# 🥇 Gold Layer — Datos de Negocio

La capa **Gold** contiene datasets preparados específicamente para **análisis de negocio y reporting**.

Ejemplos de tablas generadas:

```
sales_by_region_month
sales_by_category
sales_by_product
customer_sales_summary
```

Estas tablas están optimizadas para ser consumidas por herramientas de BI.

---

# ⚡ Funcionalidades de Delta Lake

El proyecto utiliza varias funcionalidades avanzadas de **Delta Lake**.

### ACID Transactions

Garantiza consistencia incluso con múltiples operaciones concurrentes.

### Time Travel

Permite consultar versiones anteriores de los datos.

```sql
SELECT * FROM sales VERSION AS OF 2
```

### Schema Evolution

Permite modificar el esquema sin romper pipelines.

### OPTIMIZE

Mejora el rendimiento compactando archivos.

```sql
OPTIMIZE tabla
```

### ZORDER

Optimiza el acceso a columnas específicas.

```sql
OPTIMIZE tabla
ZORDER BY (region)
```

### VACUUM

Elimina archivos obsoletos del almacenamiento.

```sql
VACUUM tabla
```

### Clonación de tablas

Delta Lake permite clonar tablas:

- **Shallow Clone** → copia solo metadata
- **Deep Clone** → copia completa de datos

---

# 🧠 Unity Catalog — Gobernanza de Datos

Unity Catalog actúa como la **capa central de gobernanza del Lakehouse**.

Permite administrar:

- Metadatos
- Permisos de acceso
- Catálogos
- Esquemas
- Tablas
- Credenciales de almacenamiento
- Ubicaciones externas
- Linaje de datos

---

## Modelo de Objetos de Unity Catalog

Unity Catalog organiza los objetos de datos de forma jerárquica:

```
Metastore
   │
   ├── Catalog
   │      │
   │      ├── Schema
   │      │      │
   │      │      ├── Tables
   │      │      ├── Views
   │      │      └── Functions
```

Ejemplo usado en el proyecto:

```
asbdevsuperstore
   │
   ├── bronze_schema
   │      └── tablas crudas
   │
   ├── silver_schema
   │      └── tablas limpias
   │
   └── gold_schema
          └── tablas analíticas
```

---

## External Locations

Unity Catalog usa **External Locations** para conectar de forma segura con **Azure Data Lake Storage**.

Ejemplo:

```sql
CREATE EXTERNAL LOCATION bronze_ext_loc
URL 'abfss://superstoredata@adsldevsuperstore.dfs.core.windows.net/bronze'
WITH (STORAGE CREDENTIAL superstore_cred);
```

Esto permite a Databricks acceder al Data Lake de forma segura.

---

## Storage Credentials

Las **Storage Credentials** permiten que Unity Catalog acceda al almacenamiento usando **Managed Identity**.

Ejemplo:

```sql
CREATE STORAGE CREDENTIAL superstore_cred
WITH AZURE_MANAGED_IDENTITY;
```

---

## Beneficios de Unity Catalog

- Gobernanza centralizada
- Control de acceso unificado
- Seguridad a nivel de tabla
- Linaje de datos
- Auditoría de acceso
- Integración con Delta Lake

---

# 🔎 Analítica con Azure Synapse Serverless

Las tablas **Gold en Delta Lake** son consultadas mediante **Azure Synapse Serverless SQL Pool**.

Synapse permite ejecutar consultas SQL directamente sobre el Data Lake **sin mover los datos**.

Ejemplo:

```sql
SELECT *
FROM OPENROWSET(
    BULK 'gold/sales_by_region_month',
    DATA_SOURCE = 'ds_superstore',
    FORMAT = 'DELTA'
) AS rows
```

Beneficios:

- consultas SQL serverless
- sin duplicación de datos
- escalabilidad automática
- pago por consulta

---

# 📊 Visualización con Power BI

Power BI se conecta a **Azure Synapse Serverless** para consumir las tablas **Gold**.

Ejemplos de análisis:

- ventas por región
- tendencias mensuales
- desempeño por producto
- métricas de negocio

Power BI actúa como la **capa semántica de analítica**.

---

# 🔐 Seguridad

La seguridad del proyecto se implementa mediante:

### Azure Key Vault

Gestión segura de secretos y credenciales.

### Managed Identity

Autenticación segura entre servicios Azure.

### Unity Catalog Permissions

Control de acceso a:

- catálogos
- esquemas
- tablas
- vistas

### RBAC en Storage

Control de acceso al Data Lake mediante roles de Azure.

---

# 🔄 Control de Versiones

El proyecto utiliza **GitHub** para:

- versionado de notebooks
- control de cambios
- colaboración
- gestión de código

Databricks se conecta directamente al repositorio mediante **Databricks Repos**.

---

# 📂 Estructura del Proyecto

```
project
│
├── notebooks
│   ├── 00_creacion_recursos
│   ├── 01_ingesta_bronze
│   ├── 02_transformacion_silver
│   └── 03_agregaciones_gold
│
├── sql
│   └── synapse_queries
│
├── dashboards
│   └── powerbi_reports
│
└── arquitectura
    └── diagrama_lakehouse
```

---

# 🎯 Objetivo del Proyecto

Este proyecto demuestra cómo construir una **plataforma moderna de Data Engineering en Azure** utilizando arquitectura Lakehouse.

Conceptos implementados:

- Lakehouse Architecture
- Medallion Data Modeling
- Delta Lake Optimization
- Data Governance con Unity Catalog
- Analítica Serverless con Synapse
- Business Intelligence con Power BI

---

# 👨‍💻 Autor

**Yonathan Montenegro Martinez**

Data Engineer | Azure | Databricks | Synapse | Power BI
