# d2iq-install-pso

# Project Title

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



```
Installing Flash Blade system
```

Connect your Flashblade using these best practices
https://support.purestorage.com/linktopractices


```
Deploying Konvoy
```

Multi step process first round you will install all applcations not requiring stateful storage
https://readthedocs.d2iq.com/instructions

```
Deploying PSO
```

Add the Pure storage repo 
helm repo add pure https://purestorage.github.io/helm-charts

Update the repo
helm repo update

Clone the help chart
git clone https://github.com/purestorage/helm-charts.git

Edit the Values file with your ARRAY variables
sample file here

Instal PSO
```
helm install --name pure-storage-driver pure/pure-csi --namespace pureflash -f ../values.yaml
```


Set the default storage class to pure-file
```
kubectl patch storageclass pure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

```
Smoke test that storage is provisined from array
```

Clone repo
https://github.com/opslounge/PSO.git

Deploy Wordpress
Observe binding applications

```
Deploy Add on Konvoy components
```

edit the cluster.yaml and set true the remaining items that need storage
prometheus
elstatic
etc

```
Deploy Jupyterhub
```

clone the following repo
https://github.com/opslounge/jupyterhub.git

you can follow the instructions in this URL to install Jupyterhub on k8s
https://github.com/opslounge/jupyterhub


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

