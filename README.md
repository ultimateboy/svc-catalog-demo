# Service Catalog Demo

This repo provides a walkthrough of the Service Catalog using only Helm charts.
The walkthrough will install:

 * Service Catalog API Server and Controller
 * An S3 Provider which includes a CF Broker for S3 bucket management
 * An S3 Consumer which uses the Broker's bindings and uploads a file to S3

## Setup Instructions

Install a Kubernetes cluster, then:

```console
helm init
cd $GOPATH/src/github.com/gabrtv/svc-catalog-demo
```

### Install Service Catalog

```
helm install \
    --namespace=steward --name=svc-catalog \
    --set registry=quay.io/kubernetes-service-catalog,version=34b14fe,storageType=tpr,debug=true,insecure=true,imagePullPolicy=IfNotPresent,globalNamespace=steward \
    svc-catalog
```

### Install the S3 Provider

```
source ~/Desktop/steward-iam.txt
echo $STEWARD_ACCESS_KEY

helm install s3-provider --namespace=steward --name=s3-provider \
    --set AdminAwsAccessKeyId=${STEWARD_ACCESS_KEY},AdminAwsSecretAccessKey=${STEWARD_SECRET_KEY}
```

### Install the S3 Consumer

```
helm install s3-consumer --namespace=steward --name=s3-consumer
```

### Cleanup

```
helm delete --purge svc-catalog
helm delete --purge s3-provider
helm delete --purge s3-consumer
kubectl delete ns steward
```