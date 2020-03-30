# d2iq-install-pso

One Paragraph of project description goes here
This page details the install steps used to install Jupyter notebook using jupyterhub for Kubernetes. to work with Flashblade and using PSO for 
provisioning

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.
These instructions will get you to a working installation of Jupyterhub on D2iq's Konvoy platform using PSO for storage provisioning and using all
storage on Pure Flash Blade


### Prerequisites

What things you need to install the software and how to install them

```
3 master nodes Centos
4 worker nodes Centos
1 Deployment Node Centos
1 Flash Blade

```

### Installing

A step by step series of examples that tell you how to get your environment up and running

Start with 7 nodes connected to flashblade
3 for mangement access API calls 
4 for storage related calls

you can use the following playbooks to prep each host. 
https://github.com/opslounge/ansibleplaybooks...


### Installing Flash Blade system


Connect your Flashblade using these best practices
https://support.purestorage.com/linktopractices



### Deploying Konvoy

Multi step process first round you will install all applcations not requiring stateful storage
https://readthedocs.d2iq.com/instructions

Set values to false before running konvoy up

```
    - name: elasticsearch
      enabled: false
    - name: elasticsearch-curator
      enabled: false
    - name: elasticsearchexporter
      enabled: false
    - name: external-dns
      enabled: false
    - name: prometheus
      enabled: false
    - name: prometheusadapter
      enabled: false
```


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

## Running the tests

Explain how to run the automated tests for this system

### Break down into end to end tests

Explain what these tests test and why

```
Give an example
```

### And coding style tests

Explain what these tests test and why

```
Give an example
```

## Deployment

Add additional notes about how to deploy this on a live system

## Built With

* [Dropwizard](http://www.dropwizard.io/1.0.2/docs/) - The web framework used
* [Maven](https://maven.apache.org/) - Dependency Management
* [ROME](https://rometools.github.io/rome/) - Used to generate RSS Feeds

## Contributing

Please read [CONTRIBUTING.md](https://gist.github.com/PurpleBooth/b24679402957c63ec426) for details on our code of conduct, and the process for submitting pull requests to us.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/your/project/tags). 

## Authors

* **Billie Thompson** - *Initial work* - [PurpleBooth](https://github.com/PurpleBooth)

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Hat tip to anyone whose code was used
* Inspiration
* etc

