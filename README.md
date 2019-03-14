# Knative and Gloo Examples

These instructions show Knative Serving and Gloo integration.

## Setup

These instructions assume you are running on a clean, recent [minikube](https://kubernetes.io/docs/setup/minikube/)
install locally.

## Install Gloo

On Mac or Linux, quickest option is to use [Homebrew](https://bash.sh)

```shell
brew install glooctl
```

Then assuming you've got a running `minikube`, and `kubectl` setup against that minikube instance, i.e. `kubectl config current-context`
returns `minikube`, run the following to install Gloo with Knative Serving 


```bash
glooctl install knative
```

## Build and deploy example 

1. Setup to use `minikube` docker daemon so example image is available to minikube.

    ```shell
    eval $(minikube docker-env)
    ```

1. Run `docker build` with your Docker Hub username

    ```shell
    docker build -t scottcranton/helloworld-go .
    
    docker push scottcranton/helloworld-go
    ```

1. Deploy the service. Again, make sure you updated username in service.yaml file

    ```shell
    kubectl apply --filename service.yaml
    ```

1. Test

    Verify domain URL for serivce. Should be `helloworld-go.default.example.com`

    ```shell
    kubectl get ksvc helloworld-go -n default  --output=custom-columns=NAME:.metadata.name,DOMAIN:.status.domain
    ```

    Call Service

    ```bash
    CLUSTERINGRESS_URL=$(glooctl proxy url --name clusteringress-proxy)
    curl -H "helloworld-knative" ${CLUSTERINGRESS_URL}
    ```

## Cleanup

```bash
kubectl delete --filename service.yaml
```

## See Also

* <https://gloo.solo.io/getting_started/kubernetes/gloo_with_knative/>
* <https://github.com/knative/docs/blob/master/install/getting-started-knative-app.md>
* <https://github.com/knative/docs>