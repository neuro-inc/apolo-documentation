# Creating a Cluster

### Introduction

Apolo lets you create a cluster in any of the three major cloud providers - [Amazon Web Services](https://aws.amazon.com/), [Google Cloud Platform](https://cloud.google.com/), and [Azure](https://azure.microsoft.com/en-in/). After you sign up with a cloud provider, you have to share your service account information and the configuration you need with our team. We will then set up a cluster on your behalf, and install Apolo on your cloud for you.

To set up a Apolo cluster, you have to do the following:

1. Set up a cloud environment:
   * GCP: [Create a project](https://cloud.google.com/appengine/docs/standard/nodejs/building-app/creating-project) and a [service account](https://cloud.google.com/iam/docs/creating-managing-service-accounts#creating).
   * AWS: [Create a VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-getting-started.html#getting-started-create-vpc) and a [service account](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html).
   * Azure: [Create a resource group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups) and a [service account](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal).
2. Prepare a cluster configuration YAML file.
3. Share the configuration YAML file with us. Once we receive the YAML file, we will set up and run the cluster (usually completed within one business day). You can start [adding your users to the cluster](managing-users-and-quotas.md) while it is being set up.

Apart from the process mentioned above, there are other methods of setup:

* Set up Apolo on an existing cluster provided by AWS, GCP, or Azure. This requires the configuration of the node pool.&#x20;
* Set up Apolo on an existing cluster provided by other cloud service providers.
* Set up Apolo on-premise (or “bare metal”).

For any of these other methods of setup, please [contact our team](mailto:support@apolo.us).

### Cluster configuration YAML&#x20;

You must create a project/VPC/resource group and a service account with all required permissions before you can start preparing a cluster configuration YAML file. The YAML file is used by the Apolo team to set up and run the cluster. You should use the `apolo admin generate-cluster-config` command to generate the YAML file. It is an interactive tool that generates a valid configuration file with the default node configuration based on your responses.

The command prompts for the following information in order to generate the configuration file:

| **Prompt**                                                                                         | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| -------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cluster Type                                                                                       | Enter cloud service provider - AWS, GCP, Azure                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| GCP Project Name or AWS VPC ID or Azure subscription ID                                            | <ul><li>For GCP, enter the name of the project.</li><li>For AWS, enter the VPC ID you created in the previous step.</li><li>For Azure, enter the Azure subscription ID you created in the previous step.</li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| <p>Service Account Key File (.json)</p><p>Or</p><p>AWS Profile Name</p><p>Or Azure information</p> | <ul><li>For GCP, enter the path to the service account key file. For information on service accounts, see <a href="https://cloud.google.com/iam/docs/understanding-service-accounts">Understanding service accounts</a>.</li><li>For AWS, enter the profile name in AWS. For more information, see <a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html">Managing Access Keys</a>.</li><li><p>For Azure, enter the following access information:</p><ul><li><a href="https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret">Azure client ID</a></li><li><a href="https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret">Azure tenant ID</a></li><li><a href="https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret">Azure client secret</a></li><li><a href="https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application">Azure resource group</a></li><li>Azure Files storage size (GB)</li></ul></li></ul> |

Below is a sample command for GCP:

```
> apolo admin generate-cluster-config
Select cluster type (aws, gcp, azure): gcp
GCP project name: My Project
Service Account Key File (.json): GCP_User.json
Cluster config cluster.yml is generated.
```

The command creates the `cluster.yml` file that includes the information required by the Apolo team to set up your cluster. Here is a sample `cluster.yml` generated for a new GCP cluster:

```
type: gcp
location_type: multi_zonal
region: us-central1
zones:
- us-central1-a
- us-central1-c
project: My Project
credentials:
 auth_provider_x509_cert_url: https://www.googleapis.com/oauth2/v1/certs
 auth_uri: https://accounts.google.com/o/oauth2/auth
 client_email: johndoe@intricate-web-236410.iam.gserviceaccount.com
 client_id: '105087309181394151560'
 client_x509_cert_url: https://www.googleapis.com/robot/v1/metadata/x509/johndoe%40intricate-web-236410.iam.gserviceaccount.com
 private_key: ...
 private_key_id: ...
 project_id: intricate-web-236410
 token_uri: https://oauth2.googleapis.com/token
 type: service_account
node_pools:
- id: n1_highmem_8
 min_size: 1
 max_size: 4
- id: n1_highmem_8_1x_nvidia_tesla_k80
 min_size: 1
 max_size: 4
- id: n1_highmem_8_1x_nvidia_tesla_v100
 min_size: 0
 max_size: 1
storage:
 id: standard
 capacity_tb: 4
```

The file contains a default nodes pools configuration that is used as a starting point:

* 1 non-preemptive node with K80
* 1 non-preemptive node with V100
* 4 non-preemptive non-GPU nodes
* and 4 Tb of standard storage

The file will create a cluster with the following presets:

* cpu-small,
* cpu-large,
* gpu-small (a node with K80), and
* gpu-large (a node with V100).

To get information about available options for each of the cloud providers, run:

`apolo admin show-cluster-options` .

You can further update the `cluster.yml` file as per your requirements before you send it to us. If you have any issues updating the file, then [contact us](mailto:support@apolo.us). Once you are done updating the configuration file, you should send the cluster.yml file to the Apolo team for the cluster setup using the command:

`apolo admin add-cluster <path/to/config>`

Once we receive the YAML file, we will set up and run the cluster (usually completed within one business day). After the command is run, you become the admin of the cluster. You can start adding users to the cluster as soon as you run the command above.
