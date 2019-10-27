# Helm

[Helm](https://github.com/helm/helm) is a tool that streamlines installing and managing Kubernetes applications. It is similar to Linux package managers such as APT and Yum. Helm makes it easy to version the deployments, package it, make a release of it, and deploy, delete, upgrade and even rollback those deployments as charts. Charts are packages of pre-configured Kubernetes resources. 
* Helm renders your templates and communicates with the Kubernetes API
* Helm runs on your laptop, CI/CD, or wherever you want it to run.
* Charts are Helm packages that contain at least two things:
    * A description of the package (Chart.yaml), as in it's name, description and version
    * One or more templates, which contain Kubernetes manifest files
* Charts can be stored on disk, or fetched from remote chart repositories

To use Helm, a server component called Tiller is installed in the Kubernetes cluster. The Tiller manages the installation of charts within the cluster. The Helm client itself is installed locally on a computer, or can be used within the Azure Cloud Shell.

![Alt Text](/images/helm.jpg)

A typical cloud-native application with a 3-tier architecture, will have the following Kubernetes objects. In this case, each tier consists of a Deployment and Service object, and may additionally define ConfigMap or Secret objects. Each of these objects are typically defined in separate YAML files, and are executed by the kubectl command line tool. A Helm chart encapsulates each of these YAML definitions, and provides a means to manage deployments as charts.

![Alt text](/images/three-tier-k8sarchitecture.jpg)





