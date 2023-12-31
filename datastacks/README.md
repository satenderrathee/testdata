# Datastacks

Datastacks is a utility built to support various functions within the Ensono Stacks Data Platform. The library and its associated Python-based CLI is intended to assist developers working within a deployed Stacks Data Platform, supporting common tasks such as generating new data engineering workloads and running Spark jobs.

### Using the Datastacks CLI

* [Setup project environment](../README.md#developing-the-solution)
```bash
# Initiate Datastacks using poetry:
poetry run datastacks

# Run Datastacks from the command line
datastacks --help

# Alternatively, run using entrypoint script
python datastacks/datastacks_cli.py --help
```

## Generating data workloads

Datastacks can be used to generate all the resources required for a new data engineering workload - for example a data ingest pipeline. This will create all resources required for the workload, based upon templates within the [de_templates](../de_templates) directory.

- **`generate`**: This command contains subcommands which generate components for the data platform given a config file.

  - **`ingest`**: This subcommand utilises the template for ingest data pipelines, and uses a given config file to generate the required code for a new ingest pipeline ready for use. A flag can be included to specify whether or not to include data quality components in the pipeline.

### Examples

```bash
# Generate resources for an ingest pipeline
datastacks generate ingest --config="de_templates/test_config_ingest.yaml"

# Generate resources for an ingest pipeline, with added Data Quality steps
datastacks generate ingest --config="de_templates/test_config_ingest.yaml" --data-quality
```

### Required config file

In order to generate a new data engineering workload the Datastacks CLI takes a path to a config file. This config file should be a yaml file and have the below format. A sample config file is included in the [de_templates](../de_templates/test_config_ingest.yaml) folder.

```yaml
# `dataset_name` parameter is used to determine names of the following ADF resources:
# - pipeline: Ingest_<dataset_name>
# - dataset: ds_<dataset_name>
# - linked service: ls_<dataset_name>
dataset_name: AzureSql_Generated_Example
pipeline_description: "Ingest from demo Azure SQL database using ingest config file."
data_source_type: azure_sql

key_vault_linked_service_name: ls_KeyVault
data_source_password_key_vault_secret_name: sql-password
data_source_connection_string_variable_name: sql_connection

# Azure DevOps configurations

ado_variable_groups_nonprod:
  - amido-stacks-de-pipeline-nonprod
  - stacks-credentials-nonprod-kv

ado_variable_groups_prod:
  - amido-stacks-de-pipeline-prod
  - stacks-credentials-prod-kv

# Datalake containers

bronze_container: raw
silver_container: staging
gold_container: curated

#######################
# Optional parameters #
#######################

# Deployment mode for terraform; if not set, the default is Incremental
default_arm_deployment_mode: Incremental

# Workload config; if not set, the default values are 2010-01-01 and 2010-01-31 resp.
# These are used to set the default time window in the pipeline and in the corresponding e2e tests
window_start_default: 2010-01-01
window_end_default: 2010-01-31
```
