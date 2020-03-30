# d2iq-install-pso

Build Jupyter as a service on Flashblade 

## Getting Started

These instructions will get you to a working installation of Jupyterhub on 
D2iq's Konvoy platform using PSO for storage provisioning and using all
storage on Pure Flash Blade

### Prerequisites

What things you need to install the software and how to install them

Systems needed for K8s on D2iq for Jupyter as a Service
Ideally you will have your Flash Blade data VIPS on a dedicated storage VLAN if possible


- 1 deployment node (used for setup cluster)
- 3 master nodes Centos  Access to Flashblade API management VLAN 
- 4 worker nodes Centos  Access to Seperate Storage VLAN 
- Pure Flash Blade 


### Installing

you can use the following playbooks to prep each host. 
```
git clone https://github.com/opslounge/centosprep.git
```
Refer to D2iq documentation for specifics around requirements based on your node types. 

https://docs.d2iq.com/ksphere/konvoy/latest/install/


### Installing Flash Blade system

Connect your Flashblade using these best practices
https://support.purestorage.com/linktopractices


### Deploying Konvoy

Details on Konvoy install are contained here
https://hackmd.io/XuoxvPGuTLeI9kpa9a1kSg?view



### Deploying PSO

Add the Pure storage repo 
```
helm repo add pure https://purestorage.github.io/helm-charts
```
Update the repo

```
helm repo update
```
Clone the helm chart
```
git clone https://github.com/purestorage/helm-charts.git
```

Edit the values file with your array information

```
arrays:
  FlashArrays:
    - MgmtEndPoint: "1.2.3.4"
      APIToken: "a526a4c6-18b0-a8c9-1afa-3499293574bb"
      Labels:
        rack: "22"
        env: "prod"
    - MgmtEndPoint: "1.2.3.5"
      APIToken: "b526a4c6-18b0-a8c9-1afa-3499293574bb"
  FlashBlades:
    - MgmtEndPoint: "1.2.3.6"
      APIToken: "T-c4925090-c9bf-4033-8537-d24ee5669135"
      NfsEndPoint: "1.2.3.7"
      Labels:
        rack: "7b"
        env: "dev"
    - MgmtEndPoint: "1.2.3.8"
      APIToken: "T-d4925090-c9bf-4033-8537-d24ee5669135"
      NfsEndPoint: "1.2.3.9"
      Labels:
        rack: "6a"
```


 Install PSO
```
helm install --name pure-storage-driver pure/pure-csi --namespace pureflash -f ../values.yaml
```


Set the default storage class to pure-file

```
kubectl patch storageclass pure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```
### Pure smoketest
Validate storage provisioning is working by using the pure-smoketest

Clone smoketest repo
```
https://github.com/opslounge/pure-smoketest.git
```
Install PVC to test storage function
you should see it bind to array 

```
kubectl get pvc
NAME              STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
smoketest-claim   Bound    pvc-d8d61813-9c88-48ce-955a-b4f448ba89e9   10Gi       RWX            pure-file      13s
```


### Konvoy Step 2 add on components

edit the cluster.yaml to install the remaining components using Flashblade for storage
Set values to true before running konvoy up 
```
    - name: elasticsearch
      enabled: true
    - name: elasticsearch-curator
      enabled: true
    - name: elasticsearchexporter
      enabled: true
    - name: external-dns
      enabled: true
    - name: prometheus
      enabled: true
    - name: prometheusadapter
      enabled: true
```


### Deploy Jupyterhub

clone the following repo
```
https://github.com/opslounge/jupyterhub.git
```
you can follow the instructions in this URL to install Jupyterhub on k8s
```
https://github.com/opslounge/jupyterhub
```
```

## Authors

* **Andy Parsons** - - [opslounge](https://github.com/opslounge)





