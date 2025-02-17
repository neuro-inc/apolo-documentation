# Managing Users and Quotas

Apolo creates a cluster based on the cluster configuration file you send to our team. The user who creates the cluster is designated the admin of the cluster. You must add users and, optionally, quotas before you can start using the nodes in the cluster. You can know the list of clusters that you can manage by running the `apolo admin get-clusters` command.

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

You can know the list of clusters that you can access by running the `apolo config get-clusters` command.

You can manage users and user quotas from the CLI. The next sections discuss how you can manage users and user quotas.

### What are the different roles available?

Apolo provides three predefined roles that you can assign to a user - `user`, `manager`, `admin`. The default role is User. The following table describes what each of the roles can do. Please note that any registered platform user may create new clusters; upon creation, a user automatically gets an Admin role in it.

| **Role** | **Description**                                                                                                                      | **Privileges**                                                                                                                                              |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| User     | The general users of the platform without special privileges. These users can manage their own and shared jobs, storage, and images. | <ul><li>Manage own and shared jobs</li><li>Manage own and shared storage</li><li>Manage own and shared images</li></ul>                                     |
| Manager  | The users who can manage all jobs, storage, and images on the cluster, as well as manage other users.                                | <ul><li>Manage all cluster jobs</li><li>Manage all cluster storage</li><li>Manage all cluster images</li><li>Manage users</li></ul>                         |
| Admin    | The same as Manager, but additionally they can change cluster configuration or remove clusters. The admins can also add node pools.  | <ul><li>Manage all cluster jobs</li><li>Manage all cluster storage</li><li>Manage all cluster images</li><li>Manage users</li><li>Remove clusters</li></ul> |

### How to add a user to a cluster?

You can add any number of users to a cluster. However, before adding a user you must decide on the role and the quota that you want to set for the user.

The role determines the access rights a user will have on the platform. The quota determines the number of GPU or CPU hours a user can use.

```
> apolo admin add-cluster-user neuro-public john user
Added john to cluster default as user
```

### How to remove a user from the cluster?

You can remove a user from the cluster if you are an Admin or Manager. You must use the `apolo admin remove-cluster-user` command to remove the user. By doing this, you revoke the user’s access to the cluster. All the entities belonging to the user are left (storage, jobs, images, etc), and you can add this user back later.

```
> apolo admin remove-cluster-user default john
Removed john from cluster default
```

### How to promote/demote a user?

You need to remove a user from a given cluster and to add them again with the desired role. All the entities belonging to the user (storage, jobs, images, etc) are not affected during removal.

### How to manage a user’s quota?

The quota determines the amount of GPU or CPU computation accessible to a user. By default, a user is given a quota of 100 credits.

You can set a user’s quota by using one of these commands:

* `apolo admin set-user-quota -c` : Use this to set the quota to a certain number of credits.

```
> apolo admin set-user-quota -c 200 default john
```

* `apolo admin add-user-quota`: Use this to add a certain number of credits to the existing user quota.

```
> apolo admin add-user-quota -c 50 default john
```

You can also set an unlimited quota for a user:

```
> apolo admin set-user-quota default john
```

You can check the quota of any user of a cluster if you are a manager or an admin of the cluster.

```
> apolo config show-quota john
```

To view your own quota, run:

```
> apolo config show-quota
```
