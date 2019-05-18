Install Minikube or MicroK8S
----------------------------

https://kubernetes.io/docs/tasks/tools/install-minikube/
https://microk8s.io/docs/

Micro K8S
~~~~~~~~~

- enable some services::

    microk8s.enable dns dashboard ingress

- alias it to kubectl if you don't use it on your personal computer::

    sudo snap alias microk8s.kubectl kubectl

- to see the dashboard, enable a proxy::

    microk8s.kubectl proxy --accept-hosts=.* --address=0.0.0.0

  and then you can go to http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/overview?namespace=default
  to have an overview of the K8S cluster.

Install Helm
------------

.. code::

    sudo snap install helm --classic
    helm init --history-max 200

Configure your local K8S cluster
--------------------------------

This sets up the Kubernetes Role Based Access Control necessary for a secure
cluster deployment.

.. code::

    kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=$EMAIL
    kubectl create serviceaccount tiller --namespace=kube-system
    kubectl create clusterrolebinding tiller --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
    helm init --service-account tiller
    kubectl --namespace=kube-system patch deployment tiller-deploy --type=json \
        --patch='[{"op": "add", "path": "/spec/template/spec/containers/0/command", "value": ["/tiller", "--listen=localhost:44134"]}]'

Configure and Install Pangeo
----------------------------

You'll need a value.yaml with correct configuration. See the example in the
docs directory of this repo. Then::

    helm repo add pangeo https://pangeo-data.github.io/helm-chart/
    helm repo update
    helm install pangeo/pangeo --name=pangeohub --namespace=pangeo --values=docs/values.yaml

To get Jupyterhub IP, enter::

    kubectl --namespace=pangeo get svc

Get the LoadBalancer cluster-ip, and just open a browser and enter it.
You can login with pangean/pangan_pwd if you've used default values.yaml.