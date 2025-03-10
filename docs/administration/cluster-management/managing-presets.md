# Managing Presets

Once your cluster's node pools are set up, you can configure resource presets that will be available for you and your team during the work process. This can be done both through the CLI and in the Apolo Console.

{% tabs %}
{% tab title="CLI" %}
**Checking your cluster's presets**

You can view your current cluster's resource presets by running `apolo config show` and referring to the **Resource Presets** section in its output:

```
Name               #CPU   Memory   Round Robin   Preemptible Node   GPU                             Jobs Avail   Credits per hour 

 cpu-small             1     4.0G                                                                          20   10               
 cpu-large             4    10.0G                                                                           8   10               
 gpu-small            23    60.0G                                  1 x nvidia-geforce-rtx-2080ti            1   10               
 gpu-large            47   120.0G                                  2 x nvidia-geforce-rtx-2080ti            0   10               
 gpu-1x3090           23    60.0G                                  1 x nvidia-geforce-rtx-3090              0   10               
 gpu-2x3090           47   120.0G                                  2 x nvidia-geforce-rtx-3090              0   10               
 cpu-medium            2     6.0G                                                                          13   10               
 cpu-micro           0.1     1.0G                                                                          83   5                
 cpu-test-storage    7.0    30.0G                                                                           2   75
```

**Modifying and adding presets**

You can easily modify or add resource presets by using the `apolo admin update-resource-preset` command.

For example, to change the amount of memory accessible through the existing **cpu-large** preset to 32GB, run:

```bash
> apolo admin update-resource-preset -m 32G company-cluster cpu-large
```

To add a new preset, just provide its name and parameters in the `apolo admin update-resource-preset` command. You can learn more about using this command [here](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/admin#update-resource-preset).

**Deleting presets**

You can delete resource presets by using the `apolo admin remove-resource-preset` command. For example:

```bash
> apolo admin remove-resource-preset company-cluster cpu-medium
```
{% endtab %}

{% tab title="Apolo Console" %}
**Checking your cluster's presets**

You can view your current cluster's resource presets in the **Information** and **Cluster management** tabs:

![](../../.gitbook/assets/image%20\(116\).png)

**Adding presets**

To add a new preset, click the **Add** icon in the Cluster management tab:

![](../../.gitbook/assets/image%20\(121\).png)

After that, enter the desired preset parameters and click the **Save** icon:

![](../../.gitbook/assets/image%20\(122\).png)

**Modifying presets**

To modify an existing preset, click the **Edit** icon next to it:

![](../../.gitbook/assets/image%20\(118\).png)

After that, enter the new parameters and click the **Save** icon:

![](../../.gitbook/assets/image%20\(117\).png)

**Deleting presets**

To delete a preset, click the **Delete** icon next to it:

![](../../.gitbook/assets/image%20\(119\).png)

After that, click the **Save** icon to confirm your action:

![](../../.gitbook/assets/image%20\(120\).png)
{% endtab %}
{% endtabs %}

###
