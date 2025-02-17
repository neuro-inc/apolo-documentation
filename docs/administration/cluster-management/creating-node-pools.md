# Creating Node Pools

### Node Pools in AWS, GCP, and Azure

As explained in the [Creating a Cluster](creating-a-cluster.md) topic, when you're creating a new platform cluster in [Amazon Web Services](https://aws.amazon.com/), [Google Cloud Platform](https://cloud.google.com/), or [Azure](https://azure.microsoft.com/en-in/), the default node pool setup that will be used as a starting point for the new cluster is determined by the cluster configuration file created by running `apolo admin generate-cluster-config`

You can check the default node pool setup in the **node\_pools** section of the `cluster.yml` file:

```
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

Once the cluster is created and set up by our team, a more detailed information about your cluster's node pools can be viewed by running the `apolo admin get-clusters` command in CLI:

```
> apolo admin get-clusters
company-cluster                                                                                      
 Status      Deployed                                                                              
 Cloud       azure                                                                                 
 Region      eastus                                                                                
 Zones       2                                                                                     
 Node pools                                                                                        
               Machine            CPU   Memory   Preemptible                     GPU   Min   Max   
              ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  
               Standard_D8_v3     7.0    28.0G        ×                                  1    10   
               Standard_NC6       5.0    50.0G        ×         1 x nvidia-tesla-k80     0    30   
               Standard_NC6       5.0    50.0G        √         1 x nvidia-tesla-k80     0    30   
               Standard_NC6s_v3   5.0   106.0G        ×        1 x nvidia-tesla-v100     0    10   
               Standard_NC6s_v3   5.0   106.0G        √        1 x nvidia-tesla-v100     0    10   
                                                                                                   
 Storage     Premium tier Azure Files with LRS replication type and 3072 GiB file share size  
```

If you need to change the your node pools, please contact our team at [support@apolo.us](mailto:support@apolo.us)

### Node Pools in an On-premise Installation

If you prefer to create an on-premise platform cluster, node pools are set up in a more individualized way from the start.

Once the set of machines required for your cluster is agreed upon, all of these machines will be grouped by their operating characteristics. Machines with identical characteristics will be combined into node pools and then given labels based on what node pool they are a part of.&#x20;

If a need to add a new machine to any of the existing node pools arises, this new machine will have to share the characteristics with other machines of the target node pool. It will also be labeled respectively to make sure it's readable and accessible as a part of this node pool. &#x20;

### Next Steps

Once the node pools are set up, you can start [configuring your cluster's usable presets](managing-presets.md). This will help create an environment streamlined specifically for your development and deployment workflow.
