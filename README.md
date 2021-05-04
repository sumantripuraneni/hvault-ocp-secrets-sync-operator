# secrets-sync-operator
An operator to deploy [secrets-syn-agent](https://github.com/sumantripuraneni/secrets-sync-agent) in a namespace to create/update the OpenShift4 secrets from Hashi Vault.

This operator provides the functionality of creating :

* A namespace `hvault-secrets-sync-operator-system` for operator deployment 
* A cluster scoped custom resource deployment (CRD) named `hvaultsecretssync`
* A deployment that makes sure a Pod is running that contains the controller part of the operator
* A container image of the operator code
* A Controller code that watches for custom resource of type `hvaultsecretssync`in a cluster and deploys [hvault-ocp-secrets-sync](https://github.com/sumantripuraneni/hvault-ocp-secrets-sync) deployment



## How does it work?

### Design 

As a pre-requirement, two configmaps needs to be created in a namespace and a serviceaccount to authenticate with Hashi Vault using KubeAuth mechanism.


Once this operator is deployed, it will be watching for the custom resources (CR) in the cluster scope (all namespaces) and when a custom resource is created by an user in a namespace, operator will deploy [secrets-sync-agent](https://github.com/sumantripuraneni/secrets-sync-agent) in a corresponding namespace, which will create secrets as per the configmaps

![Alt text](images/secrets-sync-agnet-design1.jpeg?raw=true "Create secret in same namespace") 


Supported secret types are: 

*  ImagePull Secrets
*  TLS Secrets
*  SSH Auth Secrets 
*  Opaque Secrets
*  Opaque secrets based on a template (Jinja2)


#### Examples of custom resource(CR) and configmap


An example custom resource(CR) definition

```yaml
apiVersion: secretssync.redhat.com/v1alpha1
kind: Hvaultsecretssync
metadata:
  name: hvaultsecretssync-sample-testing
  namespace: suman-hvault-01
spec:
  serviceaccount: internal-app
  loglevel: DEBUG
```

