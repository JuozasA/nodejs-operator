# nodejs-operator
demo app with ansible operator

## Prerequisites

- [git][git-tool]
- [docker][docker-tool] version 17.03+.
- [ansible][ansible-tool] version v2.6.0+
- Access to a Kubernetes v.1.9.0+ cluster.

Follow the steps in the [installation guide][install-guide] to learn how to install the Operator SDK CLI tool.

## Build Node.js image with template and push to private registry
```
oc process -f nodejs-build-template.json -p PRIVATE_REG=<> -p REGISTRY_SECRET=<> -p REGISTRY_SECRET_NAME=<>  | oc create -f -
```
where
- PRIVATE_REG              Private Regsitry URL, e.g. quay.io/org/private-image:latest                                                                                               
- REGISTRY_SECRET          Private Regsitry Secret (username:password in base64 format)
- REGISTRY_SECRET_NAME     Name of Private Registry Secret


## Build and Push operator image to container registry

Edit Node.js CustomResource so it uses the correct Node.js image from your private repo. Add/Create you private registry secret and add the name in the CR as well:
```
$ vim deploy/crds/poc.cloud-ninja.name_v1alpha1_poc_cr.yaml
apiVersion: poc.cloud-ninja.name/v1alpha1
kind: PoC
metadata:
  name: nodejs
  namespace: myapp
spec:
  # Add fields here
  size: 1
  dbname: sampledb
  dbuser: mongo-user
  dbpassword: mongo-password
  dbadminpasswd: super-admin
  myappdbname: mongodb
  weburl: app.apps.ocp.33b5.sandbox816.opentlc.com
  image: quay.io/org/private-image:latest     # Here
  regsecret: registry-pull-secret
```
## Build Operator Image and Push to private registry

```
$ operator-sdk build quay.io/example/myapp-operator:v0.0.1
$ docker login quay.io
$ docker push quay.io/example/myapp-operator:v0.0.1
```

## Deploy Operator

Make sure operator.yaml containers are pointing to the correct operator image tag!

Create resources:

```
$ oc create -f deploy/service_account.yaml
$ oc adm policy add-scc-to-user privileged system:serviceaccount:<namespace>:myapp-operator
$ oc create -f deploy/role.yaml
$ oc create -f deploy/role_binding.yaml
$ oc create -f deploy/crds/mongodb.cloud-ninja.name_nodeapps_crd.yaml
$ oc create -f deploy/crds/nodejs.cloud-ninja.name_nodeapps_crd.yaml
$ oc create -f deploy/operator.yaml
```
## Create application
```
$ oc create -f deploy/crds/mongodb.cloud-ninja.name_v1alpha1_nodeapp_cr.yaml
$ oc create -f deploy/crds/nodejs.cloud-ninja.name_v1alpha1_nodeapp_cr.yaml
```

## Delete application
```
$ oc delete mongodb mongodb
$ oc delete nodejs myapp-nodeapp
```


[operator-scope]:./../operator-scope.md
[install-guide]: ../user/install-operator-sdk.md
[layout-doc]:./project_layout.md
[homebrew-tool]:https://brew.sh/
[git-tool]:https://git-scm.com/downloads
[go-tool]:https://golang.org/dl/
[docker-tool]:https://docs.docker.com/install/
[kubectl-tool]:https://kubernetes.io/docs/tasks/tools/install-kubectl/
[minikube-tool]:https://github.com/kubernetes/minikube#installation
[ansible-tool]:https://docs.ansible.com/ansible/latest/index.html
[ansible-runner-tool]:https://ansible-runner.readthedocs.io/en/latest/install.html
[ansible-runner-http-plugin]:https://github.com/ansible/ansible-runner-http
[quay-link]:https://quay.io
