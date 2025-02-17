# On-premise Installation Guide

### Requirements:

* Kubernetes cluster:
  * It must be able to use **OpenEBS Cstor**. Disks have to be attached to Kubernetes nodes and must not be mounted or formatted.
  * If there is no internet access, each node should have a **busybox:latest** image preloaded.
* A linux VM:
  * Must be accessible by the Kubernetes cluster (this VM will host the docker registry along with the **chartmuseum** and **devpi** services, which are needed to run the platform).
  * Must have access to the Kubernetes cluster.
  * The following utilities have to be installed: **docker, kubectl, jq.**

### Archive Structure

**/chartmuseum**

* A directory with all required helm charts. It will be mounted as a volume to the **chartmuseum** container.

**/registry**

* A directory with all required docker images. It will be mounted as a volume to the **registry** container.

**/devpi**

* A directory with the **apolo-cli** python package and all its dependencies. It will be mounted as a volume to the **devpi** container.

**registry.tar**

* Saved **registry:2** image.

**chartmuseum.tar**

* Saved **chartmuseum/chartmuseum:latest** image.

**devpi.tar**

* Saved **devpi** image.

**jq.tar**

* Saved **imega/jq:latest** image, command-line JSON processor.

**yq.tar**

* Saved **mikefarah/yq:latest** image, command-line YAML processor.

**k8s/\*.yaml**

* Kubernetes resources that will be created in the cluster.

**\*.sh**

* Installation scripts.

### Platform Setup

Connect to the Linux VM and ensure that **kubectl** can connect to the Kubernetes cluster:

```
kubectl get nodes
```

Mount the USB (or external storage) device and extract the **apolo.tar** archive:

```
mkdir –p $HOME/apolo
tar -xvf apolo.tar -C $HOME/apolo
```

Prepare the config file (see [example below](on-premise-installation-guide.md#config-file-example)), run the installation script, and wait until all pods are in the Running state:

```
$HOME/apolo/install.sh $CONFIG_FILE_PATH
```

By default, if there is no Ingress certificate specified in the config file, the installation script will generate a self-signed certificate. This self-signed certificate has to be added to the certificate trust store in the platform user's development environment.

#### Configure the DNS Server

Set up A records to the platform domains **\*.neu.ro**, **default.org.neu.ro**, **\*.default.org.neu.ro**, **\*.jobs.default.org.neu.ro** in such a way that they point to all Kubernetes cluster IPv4 addresses.

### Config File Example

```
 server:
  ip: "10.240.0.8"
ui:
  type: minzdrav
ingress_ssl:
  cert_path: "/path/to/ingress.crt" # optional
  cert_key_path: "/path/to/ingress.key" # optional
postgres:
  password: changeme
  size: 10Gi
redis:
  password: changeme
  size: 10Gi
keycloak:
  username: admin
  password: changeme
auth:
  jwt_secret: changeme
registry:
  size: 10Gi
storage:
  size: 10Gi
blob_storage:
  size: 10Gi
metrics:
  size: 10Gi
node_pools:
- name: cpu
  cpu: 8
  memory_gb: 6
  disk_size_gb: 6
  nodes:
  - aks-agentpool-36699122-vmss000002
- name: gpu
  cpu: 8
  memory_gb: 6
  disk_size_gb: 6
  gpu: 1
  gpu_model: nvidia-tesla-k80
  nodes:
  - aks-agentpool-36699122-vmss000002
```

### Development Environment Setup

#### Add the certificate to the trust store (in case a self-signed certificate was generated during setup)

* Download the Ingress certificate:

```
openssl s_client -connect app.neu.ro:443 -showcerts </dev/null > ingress.crt
```

* Add it to your machine's trust store.

#### Install **Apolo** CLI

Run the following command to install Apolo CLI:

```
pip install -i http://$SERVER_IP/root/pypi apolo-cli
```
