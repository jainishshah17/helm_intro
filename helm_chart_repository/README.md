# Helm Chart Repository

### Following are few options for private Helm Chart repository. 

* [JFrog Artifactory](https://jfrog.com/container-registry/) 
* [Google Cloud Storage](https://cloud.google.com/artifact-registry/docs/helm/manage-charts)
* [Github Pages](https://github.com/helm/chart-releaser)
* [ChartMuseum Repository Server](https://help.cloudsmith.io/docs/helm-chart-repository)

**Note:** For this tutorial I am going to use JFrog Container Registry.

### Pre-requisite 
* Running Minikube or Kubernetes cluster
* Installed and configured Helm Client
* Installed and configured Kubectl

## Steps to set up JFrog Container Registry as Helm repository:

1. Run JCR using docker

```
docker run -d -p  8081:8081 -p 8082:8082 releases-docker.jfrog.io/jfrog/artifactory-jcr:7.21.12
```

2. Access JCR:
```
Access JCR opening http://localhost:8082/ in your browser
```

**Note:** Follow the instructions provided in Artifactory UI to complete setup. Use `admin/passowrd` for default credentials.

3. Create helm repositories in JCR using quick setup.
   For e.g Create following 2 repositories:
   dev-helm-local -> Local Helm repository
   dev-helm -> Virtual Helm repository which includes dev-helm-local

4. Upload package helm chart and upload it to JCR:
```
# Package helm chart
helm package ../create_chart/node-version/

# Upload chart to JCR
curl -u$ARTIFACTORY_USER:$ARTIFACTORY_PASSWORD -T node-version-0.1.0.tgz  "http://localhost:8082/artifactory/dev-helm/node-version-0.1.0.tgz"
```

5. Resolve helm chart from Artifactory JCR:
```
# Add configure helm repository
helm repo add dev-helm http://localhost:8082/artifactory/dev-helm --username $ARTIFACTORY_USER --password $ARTIFACTORY_PASSWORD

# Search helm chart from repository
helm search repo node-version

# Install node-version using helm chart from helm-dev repository
helm upgrade --install node-version dev-helm/node-version
```

**Note:** You can also deploy JCR in Kubernetes using helm. Here is link to JCR official chart [documentation](https://github.com/jfrog/charts/tree/master/stable/artifactory-jcr)
