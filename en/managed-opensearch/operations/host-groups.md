---
title: Managing {{ OS }} host groups in {{ mos-full-name }}
description: You can get a list of {{ OS }} cluster hosts and add, edit, or delete cluster host groups.
keywords:
  - Managing OpenSearch host groups
  - OpenSearch host groups
  - OpenSearch
---

# Managing {{ OS }} host groups


In a {{ mos-name }} cluster, you can manage [host groups](../concepts/host-roles.md):

* [{#T}](#list-groups).
* [{#T}](#add-host-group).
* [{#T}](#update-host-group), including adding or removing hosts.
* [{#T}](#delete-host-group).

You can also get a list of [cluster hosts](#list-hosts).

For information about migrating host groups in a {{ mos-name }} cluster to a different [availability zone](../../overview/concepts/geo-scope.md), see [this guide](host-migration.md).

## Getting a list of host groups in a cluster {#list-groups}

{% list tabs group=instructions %}

- Management console {#console}

    1. In the [management console]({{ link-console-main }}), go to the folder page and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-opensearch }}**.
    1. Click the name of the cluster you need and select the ![host-groups.svg](../../_assets/console-icons/copy-transparent.svg) **{{ ui-key.yacloud.opensearch.cluster.node-groups.title_node-groups }}** tab.

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    To get a list of host groups in a cluster, request information about the {{ OS }} cluster:

    ```bash
    {{ yc-mdb-os }} cluster get <cluster_name_or_ID>
    ```

    You can find the list of host groups in the `config.opensearch.node_groups` and `config.dashboards.node_groups` parameters.

    You can request the cluster name and ID with a [list of clusters in the folder](cluster-list.md#list-clusters).

- API {#api}

  To get a list of host groups in a cluster, use the [get](../api-ref/Cluster/get.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Get](../api-ref/grpc/Cluster/get.md) gRPC API call and provide the ID of the cluster you need in the `clusterId` request parameter.

  {% include [get-cluster-id](../../_includes/managed-opensearch/get-cluster-id.md) %}

{% endlist %}

## Creating a host group {#add-host-group}

The following limitations apply when creating host groups:

* A {{ mos-name }} cluster may contain only one `Dashboards` host group.
* If you are adding a group of `{{ OS }}` hosts and assigning the `MANAGER` role to the hosts, make sure there are at least three hosts with this role.

To create a host group:

{% list tabs group=instructions %}

- Management console {#console}

    1. In the [management console]({{ link-console-main }}), go to the folder page and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-opensearch }}**.
    1. Click the name of the cluster you need and select the ![host-groups.svg](../../_assets/console-icons/copy-transparent.svg) **{{ ui-key.yacloud.opensearch.cluster.node-groups.title_node-groups }}** tab.
    1. Click **{{ ui-key.yacloud.opensearch.cluster.node-groups.action_add-node-group }}**.
    1. Specify the group parameters:

        * [Group type](../concepts/host-roles.md): `{{ OS }}` or `Dashboards`.
        * Name which must be unique within the cluster.
        * For a `{{ OS }}` host group, select a [host role](../concepts/host-roles.md).
        * Platform, host type, and host class.

            The host class defines the technical characteristics of virtual machines that {{ OS }} nodes are deployed on. All available options are listed under [Host classes](../concepts/instance-types.md).

        * [Disk type](../concepts/storage.md) and data storage size.

            {% include [storages-step-settings](../../_includes/mdb/settings-storages-no-broadwell.md) %}

        * (Optional) Under **{{ ui-key.yacloud.mdb.cluster.section_disk-scaling }}**, set up automatic increase of disk size:

            {% include [console-autoscaling](../../_includes/mdb/mos/console_autoscaling.md) %}

        * Host distribution across availability zones and subnets.

        * Number of hosts to create.


        * Enable **{{ ui-key.yacloud.mdb.hosts.dialog.field_public_ip }}** if you want to allow [connecting](connect.md) to hosts over the internet.


    1. Click **{{ ui-key.yacloud.opensearch.cluster.node-groups.action_create-node-group }}**.

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    To create a host group, run this command:

    ```bash
    {{ yc-mdb-os }} node-group add --cluster-name <cluster_name> \
       --opensearch-node-group name=<{{ OS }}_host_group_name>,`
                              `resource-preset-id=<host_class>,`
                              `disk-size=<disk_size_in_bytes>,`
                              `disk-type-id=<disk_type>,`
                              `hosts-count=<number_of_hosts_in_group>,`
                              `zone-ids=<availability_zones>,`
                              `subnet-names=<subnet_names>,`
                              `assign-public-ip=<assign_public_address:_true_or_false>,`
                              `roles=<host_roles> \
       --dashboards-node-group name=<Dashboards_host_group_name>,`
                              `resource-preset-id=<host_class>,`
                              `disk-size=<disk_size_in_bytes>,`
                              `disk-type-id=<disk_type>,`
                              `hosts-count=<number_of_hosts_in_group>,`
                              `zone-ids=<availability_zones>,`
                              `subnet-names=<subnet_names>,`
                              `assign-public-ip=<assign_public_address:_true_or_false>
    ```

    Specify the required parameters in the command depending on what type of host group you want to create:

    {% include [cli-for-os-and-dashboards-groups](../../_includes/managed-opensearch/cli-for-os-and-dashboards-groups.md) %}

- {{ TF }} {#tf}

    1. Open the current {{ TF }} configuration file with an infrastructure plan.

        For more information about creating this file, see [Creating clusters](cluster-create.md).

        For a complete list of available {{ mos-name }} cluster configuration fields, see the [{{ TF }} provider documentation]({{ tf-provider-mos }}).

    1. To create a group of `{{ OS }}` hosts, add the `node_groups` section to `opensearch`:

        ```hcl
        resource "yandex_mdb_opensearch_cluster" "<cluster_name>" {
          ...
          config {
            opensearch {
              ...
              node_groups {
                name             = "<virtual_host_group_name>"
                assign_public_ip = <public_access>
                hosts_count      = <number_of_hosts>
                zone_ids         = ["<list_of_availability_zones>"]
                subnet_ids       = ["<list_of_subnet_IDs>"]
                roles            = ["<role_list>"]
                resources {
                  resource_preset_id = "<host_class>"
                  disk_size          = <storage_size_in_bytes>
                  disk_type_id       = "<disk_type>"
                }
              }
            }
            ...
          }
        }
        ```

        Where:

        * `assign_public_ip`: Public access to the host, `true` or `false`.
        * `roles`: `DATA` and `MANAGER` host roles.

    1. To create a group of `Dashboards` hosts, add the `dashboards` section to `config`:

        ```hcl
        resource "yandex_mdb_opensearch_cluster" "<cluster_name>" {
          ...
          config {
            ...
            dashboards {
              node_groups {
               name             = "<virtual_host_group_name>"
                assign_public_ip = <public_access>
                hosts_count      = <number_of_hosts>
                zone_ids         = ["<list_of_availability_zones>"]
                subnet_ids       = ["<list_of_subnet_IDs>"]
                resources {
                  resource_preset_id = "<host_class>"
                  disk_size          = <storage_size_in_bytes>
                  disk_type_id       = "<disk_type>"
                }
              }
            }
          }
        }
        ```

        Where `assign_public_ip` is public access to the host, `true` or `false`.

        A cluster may contain only one `Dashboards` host group.

    1. Make sure the settings are correct.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm updating the resources.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [Terraform timeouts](../../_includes/mdb/mos/terraform/timeouts.md) %}

- API {#api}

    To create an `{{ OS }}` host group, use the [addOpenSearchNodeGroup](../api-ref/Cluster/addOpenSearchNodeGroup.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/AddOpenSearchNodeGroup](../api-ref/grpc/Cluster/addOpenSearchNodeGroup.md) gRPC API call.

    To create a `Dashboards` host group, use the [addDashboardsNodeGroup](../api-ref/Cluster/addDashboardsNodeGroup.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/AddDashboardsNodeGroup](../api-ref/grpc/Cluster/addDashboardsNodeGroup.md) gRPC API call.

    Provide the group configuration under `nodeGroupSpec` in the request:

    * Host group name in the `name` parameter.
    * [Host class](../concepts/instance-types.md) in the `resources.resourcePresetId` parameter.
    * [Disk type](../concepts/storage.md) in the `resources.diskTypeId` parameter.
    * Data storage size in the `resources.diskSize` parameter.
    * Number of group hosts in the `hostsCount` parameter.
    * List of availability zones in the `zoneIds` parameter.
    * List of subnets in the `subnetIds` parameter.


    * Public access settings in the `assignPublicIp` parameter.


    * List of host roles in the `roles` parameter (for`{{ OS }}` host groups only).

{% endlist %}

## Updating a host group configuration {#update-host-group}

{% list tabs group=instructions %}

- Management console {#console}

    1. In the [management console]({{ link-console-main }}), go to the folder page and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-opensearch }}**.
    1. Click the name of the cluster you need and select the ![host-groups.svg](../../_assets/console-icons/copy-transparent.svg) **{{ ui-key.yacloud.opensearch.cluster.node-groups.title_node-groups }}** tab.
    1. Click ![image](../../_assets/console-icons/ellipsis.svg) in the line with the group and select **{{ ui-key.yacloud.opensearch.cluster.node-groups.action_edit }}**.
    1. Change host group settings:

        * [Host roles](../concepts/host-roles.md) (for `{{ OS }}` host groups only).
        * Platform, host type, and host class.

            The host class defines the technical characteristics of virtual machines that {{ OS }} nodes are deployed on. All available options are listed under [Host classes](../concepts/instance-types.md).

        * Disk size.

            Disk resizing increments depend on disk type:

            * Network HDD and SSD storage: In increments of 1 GB.


            * Local SSD storage:
                * For **Intel Cascade Lake**: In increments of 100 GB.
                * For **Intel Ice Lake**: In increments of {{ local-ssd-v3-step }}.
            * Non-replicated SSD storage: In increments of 93 GB.


        * (Optional) Under **{{ ui-key.yacloud.mdb.cluster.section_disk-scaling }}**, set up automatic increase of disk size:

            {% include [console-autoscaling](../../_includes/mdb/mos/console_autoscaling.md) %}

        * Host distribution across availability zones and subnets.

        * Number of hosts.

        * Public access to hosts.

    1. Click **{{ ui-key.yacloud.common.save }}**.

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    To change the configuration of a host group, run the command:

    ```bash
    {{ yc-mdb-os }} node-group update --cluster-name <cluster_name> \
       --node-group-name <host_group_name> \
       --resource-preset-id <host_class> \
       --disk-size <disk_size_in_bytes> \
       --hosts-count <number_of_hosts_in_group> \
       --roles <host_roles>
    ```

    Specify the required parameters in the command depending on what type of host group configuration you need:

    * `--node-group-name`: Name of the host group you want to update.
    * `--resource-preset-id`: New host class. that defines the configuration of virtual machines the {{ OS }} nodes will be deployed on. All available options are listed under [Host classes](../concepts/instance-types.md).
    * `--disk-size`: New disk size in bytes. Minimum and maximum values depend on the selected host class.
    * `--hosts-count`: New number of hosts in the group.
    * `--roles`: New [host roles](../../managed-opensearch/concepts/host-roles.md). The possible values are:

      * `data`: Assigns the `DATA` role only.
      * `manager`: Assigns the `MANAGER` role only.
      * `data+manager` or `manager+data`: Assigns both roles.

- {{ TF }} {#tf}

    1. Open the current {{ TF }} configuration file with an infrastructure plan.

        For more information about creating this file, see [Creating clusters](cluster-create.md).

        For a complete list of available {{ mos-name }} cluster configuration fields, see the [{{ TF }} provider documentation]({{ tf-provider-mos }}).

    1. To update the configuration of an `{{ OS }}` host group, edit the parameters of the required `node_groups` section in the `opensearch` section:

        ```hcl
        resource "yandex_mdb_opensearch_cluster" "<cluster_name>" {
          ...
          opensearch {
            ...
            node_groups {
              name             = "<virtual_host_group_name>"
              assign_public_ip = <public_access>
              hosts_count      = <number_of_hosts>
              roles            = ["<role_list>"]
              resources {
                resource_preset_id = "<host_class>"
                disk_size          = <storage_size_in_bytes>
                disk_type_id       = "<disk_type>"
              }
            }
            ...
          }
        }
        ```

        Where:

        * `assign_public_ip`: Public access to the host, `true` or `false`.
        * `roles`: `DATA` and `MANAGER` host roles.

    1. To update the configuration of a `Dashboards` host group, edit the `dashboards` section parameters:

        ```hcl
        resource "yandex_mdb_opensearch_cluster" "<cluster_name>" {
          ...
          dashboards {
            node_groups {
              name             = "<virtual_host_group_name>"
              assign_public_ip = <public_access>
              hosts_count      = <number_of_hosts>
              resources {
                resource_preset_id = "<host_class>"
                disk_size          = <storage_size_in_bytes>
                disk_type_id       = "<disk_type>"
              }
            }
          }
        }
        ```

        Where `assign_public_ip` is public access to the host, `true` or `false`.

    1. Make sure the settings are correct.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm updating the resources.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [Terraform timeouts](../../_includes/mdb/mes/terraform/timeouts.md) %}

- API {#api}

    To update the configuration of an `{{ OS }}` host group, use the [updateOpenSearchNodeGroup](../api-ref/Cluster/updateOpenSearchNodeGroup.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/UpdateOpenSearchNodeGroup](../api-ref/grpc/Cluster/updateOpenSearchNodeGroup.md) gRPC API call.

    To update the configuration of a `Dashboards` host group, use the [updateDashboardsNodeGroup](../api-ref/Cluster/updateDashboardsNodeGroup.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/UpdateDashboardsNodeGroup](../api-ref/grpc/Cluster/updateDashboardsNodeGroup.md) gRPC API call.

    Provide a new configuration under `nodeGroupSpec` in the request:

    * [Host class](../concepts/instance-types.md) in the `resources.resourcePresetId` parameter.
    * [Disk type](../concepts/storage.md) in the `resources.diskTypeId` parameter.
    * Data storage size in the `resources.diskSize` parameter.
    * Number of group hosts in the `hostsCount` parameter.
    * List of host roles in the `roles` parameter (for `{{ OS }}` host groups only).

    {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

## Deleting a host group {#delete-host-group}

When deleting a host group, the following limitation applies: you cannot delete a single host group with the `DATA` role.

{% list tabs group=instructions %}

- Management console {#console}

    1. In the [management console]({{ link-console-main }}), go to the folder page and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-opensearch }}**.
    1. Click the name of the cluster you need and select the ![host-groups.svg](../../_assets/console-icons/copy-transparent.svg) **{{ ui-key.yacloud.opensearch.cluster.node-groups.title_node-groups }}** tab.
    1. Click ![image](../../_assets/console-icons/ellipsis.svg) in the line with the group and select **{{ ui-key.yacloud.opensearch.cluster.node-groups.action_delete }}**.

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    To delete a host group, run the command:

    ```bash
    {{ yc-mdb-os }} node-group delete --cluster-name <cluster_name> \
       --node-group-name <host_group_name>
    ```

    In the command, specify the host group you want to delete.

- {{ TF }} {#tf}

    To remove a host group from a cluster:

    1. Open the current {{ TF }} configuration file with an infrastructure plan.

        For more information about creating this file, see [Creating clusters](cluster-create.md).

        For a complete list of available {{ mos-name }} cluster configuration fields, see the [{{ TF }} provider documentation]({{ tf-provider-mos }}).

    1. To delete an `{{ OS }}` host group, remove the corresponding `node_groups` section from the `opensearch` section.

    1. To delete a `Dashboards` host group, remove the `dashboards` section.

    1. Make sure the settings are correct.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm the deletion of resources.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [Terraform timeouts](../../_includes/mdb/mes/terraform/timeouts.md) %}

- API {#api}

    To delete an `{{ OS }}` host group, use the [deleteOpenSearchNodeGroup](../api-ref/Cluster/deleteOpenSearchNodeGroup.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/DeleteOpenSearchNodeGroup](../api-ref/grpc/Cluster/deleteOpenSearchNodeGroup.md) gRPC API call.

    To delete a `Dashboards` host group, use the [deleteDashboardsNodeGroup](../api-ref/Cluster/deleteDashboardsNodeGroup.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/DeleteDashboardsNodeGroup](../api-ref/grpc/Cluster/deleteDashboardsNodeGroup.md) gRPC API call.

    Provide the following in the request:

    * Cluster ID in the `clusterID` parameter.

      {% include [get-cluster-id](../../_includes/managed-opensearch/get-cluster-id.md) %}

    * Name of the host group to delete, in the `name` parameter.

{% endlist %}

## Getting a list of cluster hosts {#list-hosts}

{% list tabs group=instructions %}

- Management console {#console}

    1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-opensearch }}**.
    1. Click the name of the cluster you need and select the ![hosts](../../_assets/console-icons/cube.svg) **{{ ui-key.yacloud.mdb.cluster.switch_hosts }}** tab.

- API {#api}

    To get a list of cluster hosts, use the [listHosts](../api-ref/Cluster/listHosts.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/ListHosts](../api-ref/grpc/Cluster/listHosts.md) gRPC API call and provide the cluster ID in the `clusterId` request parameter.

    To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md).

{% endlist %}
