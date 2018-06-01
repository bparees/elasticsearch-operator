# elasticsearch-operator

*WORK IN PROGRESS*

Elasticsearch operator to run Elasticsearch cluster on top of Openshift and Kubernetes.
Operator uses [Operator Framework SDK](https://github.com/operator-framework/operator-sdk).

## Why use operator?

Operator is designed to provide self-service for the Elasticsearch cluster operations. See the [diagram](https://github.com/operator-framework/operator-sdk/blob/master/doc/images/Operator-Maturity-Model.png) on operator maturity model.

- Elasticsearch operator ensures proper layout of the pods
- Elasticsearch operator enables proper rolling cluster restarts
- Elasticsearch operator provides kubectl interface to manage your Elasticsearch cluster
- Elasticsearch operator provides kubectl interface to monitor your Elasticsearch cluster

## Getting started

### Kubernetes

Make sure certificates are pre-generated and deployed as secret.
Upload the Custom Resource Definition to your Kubernetes cluster:

  
    $ kubectl create -f deploy/crd.yaml

Deploy the required roles to the cluster:

    $ kubectl create -f deploy/rbac.yaml

Deploy custom resource and the Deployment resource of the operator:

    $ kubectl create -f deploy/cr.yaml
    $ kubectl create -f deploy/operator.yaml

### OpenShift

As a cluster admin apply the template with the roles and permissions:

    $ oc process -f deploy/openshift/admin-elasticsearch-template.yaml | oc apply -f

The template deploys CRD, roles and rolebindings. You can pass variables:
* `NAMESPACE` to specify which namespace's default ServiceAccount will be allowed to manage the Custom Resource.
* `ELASTICSEARCH_ADMIN_USER` to specify which user of OpenShift will be allowed to manage the Custom Resource.

In case later-on grant permissions to extra users by giving them the role `elasticsearch-operator`.

As the user which was specified as `ELASTICSEARCH_ADMIN_USER` on previous step:

Make sure the secret with Elasticsearch certificates exists and is named `<elasticsearch_cluster_name>-certs`

Then process the following template:
    $ oc process -f deploy/openshift/elasticsearch-template.yaml | oc apply -f

The template deploys the Custom Resource and the operator deployment. You can pass the following variables to the template:
* `NAMESPACE` - namespace where the Elasticsearch cluster will be deployed. Must be the same as the one specified by admin
* `ELASTICSEARCH_CLUSTER_NAME` - name of the Elasticsearch cluster to be deployed

## Customize your cluster

### Image customization

The operator is designed to work with `openshift/origin-aggregated-logging` image.

### Storage configuration

TODO

### Elasticsearch cluster topology customization

Decide how many nodes you want to run.

### Elasticsearch node configuration customization

TODO

## Limitations

Cluster administrator must set vm.max_map_count sysctl to 262144 on the host level of each node in your cluster prior to running the operator.
In case hostmounted volume is used, the directory on the host must have 777 permissions and the following selinux labels (TODO).
Certificates must be pre-generated and uploaded to the secret `<elasticsearch_cluster_name>-certs`

## Supported features

Kubernetes TBD+ and OpenShift TBD+ are supported.

- [x] SSL-secured deployment (using Searchguard)
- [ ] Index per tenant
- [ ] Logging to a file or to console
- [ ] Elasticsearch 6.x support
- [x] Elasticsearch 5.6.x support
- [ ] Master role
- [ ] Client role
- [ ] Data role
- [ ] Clientdata role
- [x] Clientdatamaster role
- [ ] Elasticsearch snapshots
- [ ] Prometheus monitoring
- [ ] Status monitoring
- [ ] Rolling restarts