# nodejs-operator
demo app with ansible operator

## Prerequisites

- [git][git-tool]
- [docker][docker-tool] version 17.03+.
- [ansible][ansible-tool] version v2.6.0+
- Access to a Kubernetes v.1.9.0+ cluster.

Follow the steps in the [installation guide][install-guide] to learn how to install the Operator SDK CLI tool.

## Build and Push operator image to container registry
```
$ operator-sdk build quay.io/example/memcached-operator:v0.0.1
$ docker push quay.io/example/memcached-operator:v0.0.1
```

## Deploy Operator
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
