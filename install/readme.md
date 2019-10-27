## Install Helm (Version used here is Helm 2.15.1)
Helm client can be installed from https://github.com/helm/helm/releases/latest. This article uses single node kubernetes cluster installed on docker desktop for linux containers on windows 10 platform with the following software versions:
```cmd
windows 10 Enterprise OS Version: 10.0.17134 N/A Build 17134
docker for windows version: 19.03.2
helm version: 2.15.1
kubernetes on docker desktop: 1.14
```
Once the binaries (helm.exe - client & tiller.exe - server) are downloaded, run the following command after setting the path in the environment variables
```cmd
# Helm client and server (tiller, if installed) versions will be listed
helm version 
# Install helm server i.e. tiller. Helm will install Tiller by reading kube config file which is used by kubectl
helm init
# Uninstall helm server
helm reset
```