# backstage-app-github-k8
backstage scaffolded application with github and k8 plugins installed.

To start the app, Update .env file with correct configurations and run:

```sh
yarn install
source .env && yarn dev
```

### Configuring backstage github auth and insights plugins.
Please update .env file with correct client and secrets. Find more info [ to configure github here](https://backstage.io/docs/auth/github/provider)

If you run into any issues, please find the documentation to [troubleshoot here](https://backstage.io/docs/auth/troubleshooting).

```properties
export AUTH_GITHUB_CLIENT_ID=<UPDATE>
export AUTH_GITHUB_CLIENT_SECRET=<UPDATE>
export GITHUB_TOKEN=<UPDATE>
```

### Configuring backstage kubernetes plugin.

You need to make sure have below configuration as part of the [app-config.yaml](app-config.yaml). This configuration is already added.

```yaml
kubernetes:
  serviceLocatorMethod:
    type: multiTenant
  clusterLocatorMethods:
    - type: config
      clusters:
        - url: ${K8S_MINIKUBE_URL}
          name: minikube
          authProvider: serviceAccount
          skipTLSVerify: true
          skipMetricsLookup: false
          serviceAccountToken: ${K8S_MINIKUBE_TOKEN}
```


Make sure you set the following environment variables.

K8S_MINIKUBE_URL - You can get the control plane url using `kubectl cluster-info` command.
K8S_MINIKUBE_TOKEN - You can get the service account token using below command. Make sure that this service account is having cluster-admin permissions so that plugin get all the cluster information.

```shell
oc -n default get secret $(oc -n default get sa deployer -o=json \
| jq -r '.secrets[0].name') -o=json \
| jq -r '.data["token"]' \
| base64 --decode

# careful if you are executing below command on production. You are giving admin permissions. 
kubectl create clusterrolebinding deployer --clusterrole=cluster-admin --serviceaccount=default:deployer 
```

### Building docker image for the Openshift/Kubernetes deployment. 

```shell
# Building the docker image from root directory.
podman image build . --tag backstage-sample-app-in-memory
podman image tag localhost/backstage-sample-app-in-memory:latest quay.io/lrangine/backstage-sample-app:1
podman image push quay.io/lrangine/backstage-sample-app:1
 
# Running the application from local docker image. 
podman run -it -p 7007:7007 -p 3000:3000 \
-e K8S_MINIKUBE_URL=<UPDATE> \
-e K8S_MINIKUBE_TOKEN=<UPDATE> \
-e AUTH_GITHUB_CLIENT_ID=<UPDATE> \
-e AUTH_GITHUB_CLIENT_SECRET=<UPDATE> \
-e GITHUB_TOKEN=<UPDATE> \
localhost/backstage-sample-app-in-memory:latest
```
