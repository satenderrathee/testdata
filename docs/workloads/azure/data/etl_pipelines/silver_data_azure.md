---
id: silver_data_azure
title: Silver Pipeline
sidebar_label: Silver Pipeline
hide_title: false
hide_table_of_contents: false
description: Silver processing pipeline
keywords:
  - silver
  - adf
  - etl
  - databricks
---

The Stacks data platform contains an example 'Silver' data processing workload, performing Bronze to Silver layer
transformations. It accepts raw data of various formats (e.g. CSV, Parquet, JSON, Delta) as an input and converts it
into a more structured Delta format.

The transformation from Bronze to Silver is crucial for filtering out the noise from raw data. It ensures that analysts
and data scientists work with a consistent, clean, and dependable dataset that can be trusted for downstream analytics.

As data moves to the Silver layer, it undergoes a series of transformations:

* **Conformance**: At this stage, inconsistencies in data representation are ironed out. For instance, disparate date
formats, value representations, or units from different data sources are standardized to a common format.
* **Cleansing**: Data errors, missing values, or duplicates, are identified and rectified. This ensures that downstream
analytics are based on clean and reliable data.
* **Enrichment**: Data from various sources might be combined or enriched with additional information to provide a more
holistic dataset.
* **Structuring**: While the Bronze layer often holds semi-structured or unstructured data, the Silver layer typically
structures this data into a more query-friendly format, enabling efficient data analysis.
* **Data Quality Checks**: This stage ensures that the data conforms to predefined quality standards, making sure it's
reliable and accurate.

Azure Data Lake Storage (ADLS Gen2) has been chosen as both the source and destination system for our data processing.

## Pipeline overview

The diagram below gives an overview of the Silver pipeline design.

![ADF_SilverPipelineDesign.png](../images/ADF_SilverPipelineDesign.png)


Silver processing is executed as Python Databricks job, with repeatable data transformation processes packaged within
our [PySparkle](../../../common/data/pysparkle/pysparkle_quickstart.md) library.

## Data Factory pipeline design

The pipelines folder is structured as follows:

![ADF_SilverPipelinesList.png](../images/ADF_SilverPipelinesList.png)

Similar to other ETL pipelines, Silver can optionally include a Data Quality validations step.
Depending on your particular needs, you can deploy each of the pipelines with or without a Data
Quality step. More on Data Quality can be found [here](data_quality_azure.md).

In ADF the base Silver pipeline is as simple as this:

![ADF_Silver.png](../images/ADF_silver.png)

It contains just one step - Python Databricks, configured to run a simple script (`silver.py`),
which is an entrypoint to the PySparkle library. PySparkle is attached to a cluster by pointing
to its location in DBFS.

Contents of `dbfs:/FileStore/scripts/silver/silver.py`:

```python
from pysparkle.pysparkle_cli import cli

def call_pysparkle_entrypoint():
    cli(["silver", "--dataset-name", "movies_dataset"], standalone_mode=False)

if __name__ == "__main__":
    call_pysparkle_entrypoint()
```
