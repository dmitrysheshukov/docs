---
title: "How to configure a {{ PG }} source endpoint in {{ data-transfer-full-name }}"
description: "In this tutorial, you will learn how to set up a {{ PG }} source endpoint in {{ data-transfer-full-name }}."
---

# Configuring {{ PG }} source endpoints

When [creating](../index.md#create) or [editing](../index.md#update) an endpoint, you can define:

* [{{ mpg-full-name }} cluster](#managed-service) connection or [custom installation](#on-premise) settings, including those based on {{ compute-full-name }} VMs. These are required parameters.
* [Additional parameters](#additional-settings).

Before you get started, check the [Service specifics for {{ PG }} sources and targets](../../../concepts/work-with-endpoints.md#postgresql).

## {{ mpg-name }} cluster {#managed-service}


{% note warning %}

To create or edit an endpoint of a managed database, you need to have the [`{{ roles.mpg.viewer }}` role](../../../../managed-postgresql/security/index.md#mpg-viewer) or the [`viewer` primitive role](../../../../iam/concepts/access-control/roles.md#viewer) assigned to the folder where this managed database cluster resides.

{% endnote %}


Connecting to the database with the cluster ID specified in {{ yandex-cloud }}.

{% list tabs group=instructions %}

- Management console {#console}

   {% include [Managed PostgreSQL UI](../../../../_includes/data-transfer/necessary-settings/ui/managed-postgresql.md) %}

- CLI {#cli}

   * Endpoint type: `postgres-source`.

   {% include [Managed PostgreSQL CLI](../../../../_includes/data-transfer/necessary-settings/cli/managed-postgresql.md) %}

- {{ TF }} {#tf}

   * Endpoint type: `postgres_source`.

   {% include [Managed PostgreSQL Terraform](../../../../_includes/data-transfer/necessary-settings/terraform/managed-postgresql.md) %}

   Here is an example of the configuration file structure:

   
   ```hcl
   resource "yandex_datatransfer_endpoint" "<endpoint_name_in_{{ TF }}>" {
     name = "<endpoint_name>"
     settings {
       postgres_source {
         security_groups = ["<list_of_security_group_IDs>"]
         connection {
           mdb_cluster_id = "<cluster_ID>"
         }
         database = "<migrated_database_name>"
         user     = "<username_for_connection>"
         password {
           raw = "<user_password>"
         }
         <additional_endpoint_settings>
       }
     }
   }
   ```


   For more information, see the [{{ TF }} provider documentation]({{ tf-provider-dt-endpoint }}).

- API {#api}

   {% include [Managed PostgreSQL API](../../../../_includes/data-transfer/necessary-settings/api/managed-postgresql.md) %}

{% endlist %}

## Custom installation {#on-premise}

For OnPremise, all fields are filled in manually.

{% list tabs group=instructions %}

- Management console {#console}

   {% include [On premise PostgreSQL UI](../../../../_includes/data-transfer/necessary-settings/ui/on-premise-postgresql.md) %}

- CLI {#cli}

   * Endpoint type: `postgres-source`.

   {% include [On premise PostgreSQL CLI](../../../../_includes/data-transfer/necessary-settings/cli/on-premise-postgresql.md) %}

- {{ TF }} {#tf}

   * Endpoint type: `postgres_source`.

   {% include [On premise PostgreSQL Terraform](../../../../_includes/data-transfer/necessary-settings/terraform/on-premise-postgresql.md) %}

   Here is an example of the configuration file structure:

   
   ```hcl
   resource "yandex_datatransfer_endpoint" "<endpoint_name_in_{{ TF }}>" {
     name = "<endpoint_name>"
     settings {
       postgres_source {
         security_groups = ["<list_of_security_group_IDs>"]
         connection {
           on_premise {
             hosts = ["<list_of_hosts>"]
             port  = <port_for_connection>
           }
         }
         database = "<migrated_database_name>"
         user     = "<username_for_connection>"
         password {
           raw = "<user_password>"
         }
         <additional_endpoint_settings>
       }
     }
   }
   ```


   For more information, see the [{{ TF }} provider documentation]({{ tf-provider-dt-endpoint }}).

- API {#api}

   {% include [On premise PostgreSQL API](../../../../_includes/data-transfer/necessary-settings/api/on-premise-postgresql.md) %}

{% endlist %}

## Additional settings {#additional-settings}

{% list tabs group=instructions %}

- Management console {#console}

   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresTableFilter.include_tables.title }}**: Data is only transferred from listed tables.

      {% include [Description for Included tables](../../../../_includes/data-transfer/fields/description-included-tables.md) %}

   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresTableFilter.exclude_tables.title }}**: Data from the listed tables is not transferred.

      The lists include the name of the [schema]({{pg-docs}}/ddl-schemas.html) (description of DB contents, structure, and integrity constraints) and the table name. Both lists support expressions in the following format:

      * `<schema_name>.<table_name>`: Fully qualified table name.
      * `<schema_name>.*`: All tables in the specified schema.

      {% include [transfer custom types PGSQL](../../../../_includes/data-transfer/custom-types-pgsql.md) %}

   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresSource.object_transfer_settings.title }}**: If required, select the DB schema elements to transfer when activating or deactivating a transfer.

   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresSourceAdvancedSettings.slot_byte_lag_limit.title }}**: Maximum size of Write-Ahead Log kept in replication slot. If exceeded, the replication process is stopped and the replication slot is deleted. The default value is 50 GB. This setting does not prevent disk overflow in the source database. You can only use it for {{ PG }} version below 13, and we recommend [monitoring the WAL slot value](../../prepare.md#source-pg) in the source database.

   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresSourceAdvancedSettings.service_schema.title }}**: Specify the name of the schema to store service tables (`__consumer_keeper` and `__data_transfer_mole_finder`).

   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresSourceAdvancedSettings.collapse_inherit_table.title }}**: Select to merge the contents of tables. For more information, see [Specifics of working with endpoints](../../../concepts/work-with-endpoints.md#postgresql).

   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresSourceAdvancedSettings.snapshot_table_sharding_settings.title }}**: If necessary, set detailed settings for parallel copying of tables (if parallel copying parameters are set in the transfer).

- CLI {#cli}

   * `--include-table`: List of included tables. If this is on, the data will only be transferred from the tables in this list.

      {% include [Description for Included tables](../../../../_includes/data-transfer/fields/description-included-tables.md) %}

   * `--exclude-table`: List of excluded tables. Data from the listed tables will not be transferred.

      The lists include the name of the schema (description of DB contents, structure, and integrity constraints) and the table name. Both lists support expressions in the following format:

      * `<schema_name>.<table_name>`: Fully qualified table name.
      * `<schema_name>.*`: All tables in the specified schema.

      {% include [transfer custom types PGSQL](../../../../_includes/data-transfer/custom-types-pgsql.md) %}

   * `--slot-lag-limit`: Maximum size of the write-ahead log kept in the replication slot. If exceeded, the replication process is stopped and the replication slot is deleted. The default value is 50 GB.

   * `--service-schema`: Name of the DB schema for service tables.

   * Schema transfer settings:

      * `--transfer-before-data`: At the initial stage of the transfer.
      * `--transfer-after-data`: At the final stage of the transfer.

- {{ TF }} {#tf}

   * `include_tables`: List of included tables. If this is on, the data will only be transferred from the tables in this list.

      {% include [Description for Included tables](../../../../_includes/data-transfer/fields/description-included-tables.md) %}

   * `exclude_tables`: List of excluded tables. Data from the listed tables will not be transferred.

      The lists include the name of the schema (description of DB contents, structure, and integrity constraints) and the table name. Both lists support expressions in the following format:

      * `<schema_name>.<table_name>`: Fully qualified table name.
      * `<schema_name>.*`: All tables in the specified schema.

      {% include [transfer custom types PGSQL](../../../../_includes/data-transfer/custom-types-pgsql.md) %}

   * `slot_gigabyte_lag_limit`: Maximum size of Write-Ahead Log kept in replication slot. If exceeded, the replication process is stopped and the replication slot is deleted. The default value is 50 GB.

   * `service_schema`: DB schema name for housekeeping tables.

   * `object_transfer_settings`: Schema transfer settings:

      * `sequence`: Sequences.
      * `sequence_owned_by`: User sequences.
      * `table`: Tables.
      * `primary_key`: Primary keys.
      * `fk_constraint`: Foreign keys.
      * `default_values`: Default values.
      * `constraint`: Constraints.
      * `index`: Indexes.
      * `view`: Views.
      * `function`: Functions.
      * `trigger`: Triggers.
      * `type`: Types.
      * `rule`: Rules.
      * `collation`: Collation rules.
      * `policy`: Policies.
      * `cast`: Type casts.

      You can specify one of the following values for each entity:

      * `BEFORE_DATA`: Move at transfer activation.
      * `AFTER_DATA`: Move at transfer deactivation.
      * `NEVER`: Do not move.

   For more information, see the [{{ TF }} provider documentation]({{ tf-provider-dt-endpoint }}).

- API {#api}

   * `includeTables`: List of included tables. If this is on, the data will only be transferred from the tables in this list.

      {% include [Description for Included tables](../../../../_includes/data-transfer/fields/description-included-tables.md) %}

   * `excludeTables`: List of excluded tables. Data from the listed tables will not be transferred.

      The lists include the name of the schema (description of DB contents, structure, and integrity constraints) and the table name. Both lists support expressions in the following format:

      * `<schema_name>.<table_name>`: Fully qualified table name.
      * `<schema_name>.*`: All tables in the specified schema.

      {% include [transfer custom types PGSQL](../../../../_includes/data-transfer/custom-types-pgsql.md) %}

   * `slotByteLagLimit`: Maximum size of the write-ahead log kept in the replication slot. If exceeded, the replication process is stopped and the replication slot is deleted. The default value is 50 GB.

   * `serviceSchema`: Name of the DB schema for service tables.

   * `objectTransferSettings`: Settings for transferring the schema at the initial and final stages of the transfer (`BEFORE_DATA` and `AFTER_DATA` values, respectively).

{% endlist %}

### Settings for transferring a DB schema when enabling and disabling a transfer {#schema-migrations-settings}

{% note info %}

The default settings of the source endpoint allow you to successfully perform a transfer for most databases. Change the settings of the initial and final stages of the transfer only if it is necessary.

{% endnote %}

During a transfer, the database schema is transferred from the source to the target. The transfer is performed in two stages:

1. At the activation stage.

   This step is performed when the transfer is activated before copying or replicating data to create a schema on the target. You can choose which parts of the schema will be migrated. By default, this is `TABLE`, `VIEW`, `PRIMARY KEY`, `SEQUENCE`, `SEQUENCE OWNED BY`, `RULE`, `TYPE`, `FUNCTION`, and `DEFAULT`.

1. At the deactivation stage.

   This step is performed at the end of the transfer operation when it is deactivated. If the transfer keeps running in replication mode, the final stage of the transfer will be performed only when replication stops. You can choose which parts of the schema will be migrated.

   At the final stage, it is assumed that when the transfer is deactivated, there is no writing load on the source. You can ensure this by switching to <q>read-only</q> mode. At this stage, the database schema on the target is brought to a state where it will be consistent with the schema on the source.

   It's recommended to include resource-intensive operations like index migration in the final stage of migration. Migrating indexes at the beginning of the transfer can slow it down.

The transfer of the schema at both the initial and final stages is performed using the `pg_dump` [utility](https://www.postgresql.org/docs/current/app-pgdump.html).

{% note info %}

When editing the settings of an endpoint of the transfer in the {{ dt-status-repl }} status, the table schemas on the target are preserved. In this case, only the schemas of the tables that are missing on the target at the time of restart are transferred to the target.

{% endnote %}

Replication cannot guarantee that sequence values are preserved, so we recommend updating the `sequences` on the target.
