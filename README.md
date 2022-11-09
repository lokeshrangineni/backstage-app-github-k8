# backstage-app-github-k8
backstage scaffolded application with github and k8 plugins installed.

To start the app, Update .env file with correct configurations and run:

```sh
yarn install
source .env && yarn dev
```

```shell
#Building the docker image from root directory.
podman image build . --tag backstage-sample-app-in-memory
podman image tag localhost/backstage-sample-app-in-memory:latest quay.io/lrangine/backstage-sample-app:1
podman image push quay.io/lrangine/backstage-sample-app:1
 
#Running the application from docker image. 
podman run -it -p 7007:7007 -p 3000:3000 K8S_MINIKUBE_URL=<> -e AUTH_GITHUB_CLIENT_ID=<AUTH_GITHUB_CLIENT_ID> -e AUTH_GITHUB_CLIENT_SECRET=<AUTH_GITHUB_CLIENT_SECRET> -e GITHUB_TOKEN=<GITHUB_TOKEN> localhost/backstage-sample-app-in-memory:latest
```
