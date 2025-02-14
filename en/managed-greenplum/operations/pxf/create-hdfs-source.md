# Creating an external HDFS data source

In {{ mgp-name }}, as an [external data source](../../concepts/external-tables.md#pxf-data-sources) with the HDFS connection type, you can use HDFS as part of [{{ dataproc-full-name }}](../../../data-proc/index.yaml) or other third-party HDFS services.

## Create an external data source {#create-external-source}

{% list tabs group=instructions %}

- Management console {#console}

    1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
    1. Open the page of the {{ mgp-name }} cluster you need.
    1. In the left-hand panel, select ![image](../../../_assets/console-icons/arrow-right-arrow-left.svg) **{{ ui-key.yacloud.greenplum.label_pxf }}**.
    1. Click **{{ ui-key.yacloud.greenplum.cluster.pxf.action_create-datasource }}**.
    1. Select the `{{ ui-key.yacloud.greenplum.cluster.pxf.value_hdfs }}` connection type.
    1. Enter a source name.
    1. Configure at least one [optional setting](../../concepts/settings-list.md#hdfs-settings).
    1. Click **{{ ui-key.yacloud.common.create }}**.

- API {#api}

    To add an HDFS data source to a {{ mgp-name }} cluster, use the [create](../../api-ref/PXFDatasource/create.md) REST API method for the [PXFDatasource](../../api-ref/PXFDatasource/index.md) resource or the [PXFDatasourceService/Create](../../api-ref/grpc/PXFDatasource/create.md) gRPC API call and provide the following in the request:

    * Cluster ID in the `clusterId` parameter. To find out the cluster ID, [get a list of clusters in the folder](../cluster-list.md#list-clusters).
    * Data source name in the `name` parameter.
    * External source settings in the `hdfs` parameter.

{% endlist %}

## Example of a REST API request {#example}

The example below shows how to create an external HDFS data source using the {{ mgp-name }} REST API. To create a source:

1. [Get an IAM token](../../../iam/operations/index.md#iam-tokens). It is used for authentication in the API.
1. Add the IAM token to the following environment variable:

    ```bash
    export IAM_TOKEN=<token>
    ```

1. Send a request using [cURL](https://curl.haxx.se):

    ```bash
    curl --location "https://mdb.{{ api-host }}/managed-greenplum/v1/clusters/<cluster_ID>/pxf_datasources" \
        --header "Content-Type: text/plain" \
        --header "Authorization: Bearer ${IAM_TOKEN}" \
        --data "{
            \"datasource\": {
                \"name\": \"hdfs:csv\",
                \"hdfs\": {
                    \"core\": {
                        \"defaultFs\": \"<storage_type:_DISK_or_ARCHIVE>\"
                    }
                }
            }
        }"
    ```

    In the request body, specify the following parameters:

    * `name`: Source name, for example, `hdfs:csv`.
    * `defaultFs`: Default data storage type (optional). The possible values are:

        * `DISK`: Data storage on a physical disk.
        * `ARCHIVE`: Data storage in archive. In this case, you can store more data in HDFS, but their processing speed will be lower.

{% include [greenplum-trademark](../../../_includes/mdb/mgp/trademark.md) %}
