# Node.js example app for Kyma deployment with Azure Container Registry

Example app based on the SAP tutorial for [Deploy a Node.js Application in the Kyma Runtime](https://developers.sap.com/tutorials/deploy-nodejs-application-kyma.html#f3a8d4a0-5ae4-4eaa-bfab-62cced789ff2).

> It should be nearly the same procesure for a .NET application.

## Used tools

* [Azure Container Registry](https://learn.microsoft.com/en-us/azure/container-registry/container-registry-intro)
* [SAP BTP](https://www.sap.com/germany/products/technology-platform/trial.html)
* [Kyma](https://kyma-project.io/)
* [Chocolatey for installing pack and kubectl](https://chocolatey.org/install), you could install it with 
``` powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1')) 
``` 

* [Pack](https://buildpacks.io/docs/tools/pack/)
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)
* [Docker Desktop](https://docs.docker.com/desktop/install/windows-install/)


## Main steps

* Developing your app
* Configure your Kyma namespace and development environment
* Connect your Kyma runtime to container registry
    * `kubectl -n multitenancy-ns create secret docker-registry registry-secret --docker-server=https://XXXXXX.azurecr.io/  --docker-username=XXXXXX --docker-password=XXXXXX`
* Build a docker image ([OCI image](https://github.com/opencontainers/image-spec))
    * `pack build multitenant-kyma-backend --builder paketobuildpacks/builder:full`
* Tag and push the image
    * `docker tag multitenant-kyma-backend mydockerhub001.azurecr.io/multitenant-kyma-backend:v2`
    * `docker push mydockerhub001.azurecr.io/multitenant-kyma-backend:v2`
* Prepare your Kubernetes configuration file, with the fellowing informations
    * Subaccount ID (Subaccount):
XXX-XXX-XXX-XXX-XXX
    * Subdomain:
abXXXXXXtrial
    * Kubernetes cluster domain: api.XXXXXX.kyma.ondemand.com (derived from API Server Address: https://api.XXXXXX.kyma.ondemand.com)
* Create a pod in Kyma
    * `kubectl -n multitenancy-ns apply -f k8s-deployment-backend.yaml`