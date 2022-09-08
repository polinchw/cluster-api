# cluster-api

## Description

This repo has some example Kubernetes cluster-api objects for creating clusters with [cluster-api](https://cluster-api.sigs.k8s.io/).

### Providers

#### Azure

##### Azure Provider

To initialize the management cluster to work with Azure run the following.

```
export AZURE_SUBSCRIPTION_ID="<SubscriptionId>"

# Create an Azure Service Principal and paste the output here
export AZURE_TENANT_ID="<Tenant>"
export AZURE_CLIENT_ID="<AppId>"
export AZURE_CLIENT_SECRET="<Password>"

# Base64 encode the variables
export AZURE_SUBSCRIPTION_ID_B64="$(echo -n "$AZURE_SUBSCRIPTION_ID" | base64 | tr -d '\n')"
export AZURE_TENANT_ID_B64="$(echo -n "$AZURE_TENANT_ID" | base64 | tr -d '\n')"
export AZURE_CLIENT_ID_B64="$(echo -n "$AZURE_CLIENT_ID" | base64 | tr -d '\n')"
export AZURE_CLIENT_SECRET_B64="$(echo -n "$AZURE_CLIENT_SECRET" | base64 | tr -d '\n')"

# Settings needed for AzureClusterIdentity used by the AzureCluster
export AZURE_CLUSTER_IDENTITY_SECRET_NAME="cluster-identity-secret"
export CLUSTER_IDENTITY_NAME="cluster-identity"
export AZURE_CLUSTER_IDENTITY_SECRET_NAMESPACE="default"

# Create a secret to include the password of the Service Principal identity created in Azure
# This secret will be referenced by the AzureClusterIdentity used by the AzureCluster
kubectl create secret generic "${AZURE_CLUSTER_IDENTITY_SECRET_NAME}" --from-literal=clientSecret="${AZURE_CLIENT_SECRET}" --namespace "${AZURE_CLUSTER_IDENTITY_SECRET_NAMESPACE}"

# Finally, initialize the management cluster
clusterctl init --infrastructure azure
```
##### Azure Worker

To create an AKS 'managed' cluster run the following command.  The --flavor aks is critical in having the cluster
manged by AKS.  If the --flavor flag is not inclued a 'regular' cluster will be created.

```
clusterctl generate cluster capi-quickstart --flavor aks --kubernetes-version v1.23.8 --worker-machine-count=2 > azure.yaml

```

After that command runs you can download the new cluster's kube context file.  This command will add
the new cluster's context to your ~/.kube/config file.

```
az aks get-credentials --resource-group capi-quickstart --name capi-quickstart
```
##### Delete Azure Worker

To delete the cluster run the following command:

```
kubectl delete cluster capi-quickstart
```

