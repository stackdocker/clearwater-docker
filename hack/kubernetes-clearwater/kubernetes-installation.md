Kubernetes DevOps
==================

Tables of content
------------------

* Prerequisites

* Binaries

>* Download release

>* Build from source

* Generate CA and certs

* Systemd services - master

>* Install kube-apiserver

>* Install kube-controller-manager server

>* Install kube-scheduler server

>* Install kubelet server

>* Install kube-proxy server

* Load POD image

* Kubernetes addons

>* Start kube-dns

>* Start kubernetes-dashboard

* Install node - worker

Prerequisites
--------------

Host firewall

    [vagrant@localhost ~]$ sudo systemctl disable firewalld.service

Etcd must be installed

Docker is required

软件的可执行程序文件
-------------------

### Download release

For example

    [vagrant@localhost ~]$ wget -c https://github.com/kubernetes/kubernetes/releases/download/v1.3.10/kubernetes.tar.gz
    --2016-11-11 01:58:59--  https://github.com/kubernetes/kubernetes/releases/download/v1.3.10/kubernetes.tar.gz
    Resolving github.com (github.com)... 192.30.253.113, 192.30.253.112
    Connecting to github.com (github.com)|192.30.253.113|:443... connected.
    HTTP request sent, awaiting response... 302 Found
    Location: https://github-cloud.s3.amazonaws.com/releases/20580498/b73a52cc-9f80-11e6-8085-e5bdc11f8bd8.gz?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAISTNZFOVBIJMK3TQ%2F20161111%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20161111T015900Z&X-Amz-Expires=300&X-Amz-Signature=77d9df5701f07bd4922654ac7aad99ba4a1aa65d32c2cc8c258170b710511645&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dkubernetes.tar.gz&response-content-type=application%2Foctet-stream [following]
    --2016-11-11 01:59:00--  https://github-cloud.s3.amazonaws.com/releases/20580498/b73a52cc-9f80-11e6-8085-e5bdc11f8bd8.gz?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAISTNZFOVBIJMK3TQ%2F20161111%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20161111T015900Z&X-Amz-Expires=300&X-Amz-Signature=77d9df5701f07bd4922654ac7aad99ba4a1aa65d32c2cc8c258170b710511645&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dkubernetes.tar.gz&response-content-type=application%2Foctet-stream
    Resolving github-cloud.s3.amazonaws.com (github-cloud.s3.amazonaws.com)... 52.216.0.128
    Connecting to github-cloud.s3.amazonaws.com (github-cloud.s3.amazonaws.com)|52.216.0.128|:443... connected.
    HTTP request sent, awaiting response... 206 Partial Content
    Length: 1495467451 (1.4G), 1495466858 (1.4G) remaining [application/octet-stream]
    Saving to: 鈥榢ubernetes.tar.gz鈥▒

    27% [=========>                             ] 405,582,941 3.21MB/s  eta 8m 34s

    [vagrant@localhost ~]$ sudo tar -C /opt -zxf kubernetes.tar.gz kubernetes/server/kubernetes-server-linux-amd64.tar.gz

    [vagrant@localhost ~]$ sudo tar -tzf /opt/kubernetes/server/kubernetes-server-linux-amd64.tar.gz
    kubernetes/
    kubernetes/addons/
    kubernetes/kubernetes-src.tar.gz
    kubernetes/server/
    kubernetes/server/bin/
    kubernetes/server/bin/kube-proxy.tar
    kubernetes/server/bin/kube-controller-manager
    kubernetes/server/bin/kube-proxy.docker_tag
    kubernetes/server/bin/federation-apiserver.docker_tag
    kubernetes/server/bin/federation-controller-manager.docker_tag
    kubernetes/server/bin/kube-scheduler
    kubernetes/server/bin/kube-scheduler.docker_tag
    kubernetes/server/bin/kube-controller-manager.tar
    kubernetes/server/bin/kubectl
    kubernetes/server/bin/kube-apiserver.docker_tag
    kubernetes/server/bin/federation-apiserver.tar
    kubernetes/server/bin/kube-apiserver.tar
    kubernetes/server/bin/federation-controller-manager.tar
    kubernetes/server/bin/federation-apiserver
    kubernetes/server/bin/kube-dns
    kubernetes/server/bin/kubemark
    kubernetes/server/bin/kubelet
    kubernetes/server/bin/kube-proxy
    kubernetes/server/bin/kube-scheduler.tar
    kubernetes/server/bin/kube-apiserver
    kubernetes/server/bin/kube-controller-manager.docker_tag
    kubernetes/server/bin/federation-controller-manager
    kubernetes/server/bin/hyperkube
    kubernetes/LICENSES

    [vagrant@localhost ~]$ sudo tar -C /opt -zxf /opt/kubernetes/server/kubernetes-server-linux-amd64.tar.gz

    [vagrant@localhost ~]$ sudo ls /opt/kubernetes/
    1.3.10  addons  kubernetes-src.tar.gz  LICENSES  server

### Build (development option)

使用git

    [tangfx@localhost k8s.io]$ git clone https://github.com/tangfeixiong/kubernetes kubernetes
    Cloning into 'kubernetes'...
    remote: Counting objects: 337701, done.
    remote: Compressing objects: 100% (5/5), done.
    Receiving objects:  19% (65526/337701), 72.23 MiB | 127.00 KiB/s

    [tangfx@localhost k8s.io]$ git remote add upstream https://github.com/kubernetes/kubernetes

    [tangfx@localhost k8s.io]$ git pull upstream

    [tangfx@localhost kubernetes]$ git tag --list

检出v1.3.10

    [tangfx@localhost kubernetes]$ git checkout v1.3.10
    Note: checking out 'v1.3.10'.

    You are in 'detached HEAD' state. You can look around, make experimental
    changes and commit them, and you can discard any commits you make in this
    state without impacting any branches by performing another checkout.

    If you want to create a new branch to retain commits you create, you may
    do so (now or later) by using -b with the checkout command again. Example:

      git checkout -b <new-branch-name>

    HEAD is now at c3e367e... Kubernetes version v1.3.10

    [tangfx@localhost kubernetes]$ git status
    HEAD detached at v1.3.10
    nothing to commit, working directory clean

Golang语言

    [tangfx@localhost kubernetes]$ go version
    go version go1.6.2 linux/amd64

Make
    [tangfx@localhost kubernetes]$ make all GOFLAGS=-v
    hack/build-go.sh
    Go version: go version go1.6.2 linux/amd64
    +++ [1109 15:43:03] Building the toolchain targets:
        k8s.io/kubernetes/hack/cmd/teststale
    k8s.io/kubernetes/vendor/github.com/golang/glog
    k8s.io/kubernetes/hack/cmd/teststale
    +++ [1109 15:43:05] Building go targets for linux/amd64:
        cmd/kube-dns
        cmd/kube-proxy
        cmd/kube-apiserver
        cmd/kube-controller-manager
        cmd/kubelet
        cmd/kubemark
        cmd/hyperkube
        federation/cmd/federation-apiserver
        federation/cmd/federation-controller-manager
        plugin/cmd/kube-scheduler
        cmd/kubectl
        cmd/integration
        cmd/gendocs
        cmd/genkubedocs
        cmd/genman
        cmd/genyaml
        cmd/mungedocs
        cmd/genswaggertypedocs
        cmd/linkcheck
        examples/k8petstore/web-server/src
        federation/cmd/genfeddocs
        vendor/github.com/onsi/ginkgo/ginkgo
        test/e2e/e2e.test
        test/e2e_node/e2e_node.test
    +++ [1109 15:43:05] +++ Warning: stdlib pkg with cgo flag not found.
    +++ [1109 15:43:05] +++ Warning: stdlib pkg cannot be rebuilt since /usr/local/go/pkg is not writable by tangfx
    +++ [1109 15:43:05] +++ Warning: Make /usr/local/go/pkg writable for tangfx for a one-time stdlib install, Or
    +++ [1109 15:43:05] +++ Warning: Rebuild stdlib using the command 'CGO_ENABLED=0 go install -a -installsuffix cgo std'
    +++ [1109 15:43:05] +++ Falling back to go build, which is slower
    **********************
    +++ [1109 15:41:00] Placing binaries

    [vagrant@localhost kubernetes]$ ls _output/bin/
    conversion-gen                 integration
    deepcopy-gen                   kube-apiserver
    defaulter-gen                  kube-controller-manager
    e2e_node.test                  kubectl
    e2e.test                       kube-dns
    federation-apiserver           kubelet
    federation-controller-manager  kubemark
    gendocs                        kube-proxy
    genfeddocs                     kube-scheduler
    genkubedocs                    linkcheck
    genman                         mungedocs
    genswaggertypedocs             openapi-gen
    genyaml                        src
    ginkgo                         teststale
    hyperkube

    [vagrant@localhost kubernetes]$ _output/bin/kubelet --version
    Kubernetes v1.3.10-dirty

    [vagrant@localhost kubernetes]$ _output/bin/kubectl version --client
    Client Version: version.Info{Major:"1", Minor:"3+", GitVersion:"v1.3.10-dirty", GitCommit:"c3e367ec9eae7338ac4e2a57f293634891319b7c", GitTreeState:"dirty", BuildDate:"2016-11-09T15:11:23Z", GoVersion:"go1.6.2", Compiler:"gc", Platform:"linux/amd64"}

Place binaries

    [vagrant@localhost ~]$ sudo chown vagrant: /opt/kubernetes/1.3.10

    [vagrant@localhost ~]$ mkdir bin

    [vagrant@localhost ~]$ ln -s /opt/kubernetes/1.3.10/{kubectl,kubelet,kube-apiserver,kube-controller-manager,kube-scheduler,kube-proxy,hyperkube} bin

Generate CA and Certs
------------------------

Using [saltbase make-ca-cert.sh](https://github.com/kubernetes/kubernetes/tree/master/cluster/saltbase/salt/generate-cert)

easy-rsa reference => https://github.com/OpenVPN/easy-rsa

    [vagrant@localhost ~]$ ls . kube
    .:
    bin  kube  kubernetes  salt-make-ca-cert.sh

    kube:
    easy-rsa.tar.gz

    [vagrant@localhost ~]$ ./salt-make-ca-cert.sh
    IP:10.64.33.81,IP:10.123.240.1,DNS:kubernetes,DNS:kubernetes.default,DNS:kubernetes.default.svc,DNS:kubernetes.default.svc.cluster.local
    Create ca certs into /srv/kubernetes

    [vagrant@localhost ~]$ ls /srv/kubernetes
    ca.crt  kubecfg.crt  kubecfg.key  server.cert  server.key

Systemd service
----------------

### Run all components as systemd services

Reference

    https://github.com/kubernetes/kubernetes/tree/master/cluster/centos/master/scripts

* The kube-apiserver

Service file

    [vagrant@localhost ~]$ vi kubernetes/1.3.10/centos/systemd/kube-apiserver.service
    [Unit]
    Description=Kubernetes API server
    Documentation=https://github.com/kubernetes/kubernetes
    After=etcd.service
    Requires=etcd.service
    # Wants=
    # Conflicts=openshift-master.service

    [Service]
    Type=notify
    NotifyAccess=all
    # Type=simple
    User=root

    Environment=KUBERNETES_BUILD_VERSION=1.3.10
    EnvironmentFile=/etc/sysconfig/kube-apiserver
    WorkingDirectory=/opt/kubernetes

    ExecStart=/opt/kubernetes/server/bin/kube-apiserver $KUBE_APISERVER_OPTS
    # ExecStart=/bin/bash -c "/opt/kubernetes/${KUBERNETES_BUILD_VERSION}/centos/bin/kube-apiserver ${KUBE_APISERVER_OPTS}"

    Restart=always
    # Restart=on-failure

    [Install]
    WantedBy=multi-user.target

Opts file

    [vagrant@localhost ~]$ vi kubernetes/1.3.10/centos/systemd/conf/kube-apiserver
    # KUBE_APISERVER_VERSION=1.3.10

    # --admission-control=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,ResourceQuota

    # --advertise-address=<nil>: IP address to advertise the apiserver to members of the cluster, must be reachable by the cluster
    # ADVERTISE_APISERVER=10.64.33.81

    # --cert-dir="/var/run/kubernetes"
    # CERT_DIR=/srv/kubernetes

    # --enable-swagger-ui[=false]: Enables swagger ui at /swagger-ui
    # ENABLE_SWAGGER_UI=false

    # --etcd-servers=: comma separated etcd servers to connect (http://ip:port)
    # ETCD_SERVERS=http://10.64.33.81:2379

    # --master-service-namespace="default": in which the kubernetes master services should be injected into pods

    # --runtime-config=: A set of key=value pairs such as apis/<groupVersion>=true/false, also  apis/<groupVersion>/<resource>
    # RUNTIME_CONFIG=api/all=true

    # --secure-port=6443: The port on which to serve HTTPS
    # SECURE_PORT=6443

    # --service-cluster-ip-range=<nil>: A CIDR notation IP range
    # Same as GKE, cluster CIDR: 10.120.0.0/14, service CIDR: 10.123.240.0/20
    # CLUSTER_CIDR=10.120.0.0/14
    # SERVICE_CIDR=10.123.240.0/20
    # CLUSTER_MASTER=10.123.240.1
    # CLUSTER_DNS=10.123.240.10

    # --service-node-port-range=: default '30000-32767'

    KUBE_APISERVER_OPTS="--admission-control=AlwaysAdmit \
      --advertise-address=10.64.33.81 \
      --allow-privileged=true \
      --apiserver-count=1 \
      --bind-address=0.0.0.0 \
      --cert-dir=/srv/kubernetes \
      --client-ca-file=/srv/kubernetes/ca.crt \
      --enable-swagger-ui=false \
      --etcd-servers=http://10.64.33.81:2379 \
      --insecure-bind-address=127.0.0.1 \
      --insecure-port=8080 \
      --master-service-namespace=default \
      --runtime-config=api/all=true \
      --secure-port=6443 \
      --service-cluster-ip-range=10.123.240.0/20 \
      --tls-cert-file=/srv/kubernetes/server.crt \
      --tls-private-key-file=/srv/kubernetes/server.key \
      --v=2"

Manually install

    [vagrant@localhost ~]$ sudo cp kubernetes/1.3.10/centos/systemd/conf/kube-apiserver  /etc/sysconfig/

    [vagrant@localhost ~]$ sudo cp kubernetes/1.3.10/centos/systemd/system/kube-apiserver.service  /etc/systemd/system

    [vagrant@localhost ~]$ sudo systemctl enable kube-apiserver.service
    Created symlink from /etc/systemd/system/multi-user.target.wants/kube-apiserver.service to /etc/systemd/system/kube-apiserver.service.

Experimental run

    [vagrant@localhost ~]$ KUBE_APISERVER_OPTS="--admission-control=AlwaysAdmit \
    >       --advertise-address=10.64.33.81 \
    >       --allow-privileged=true \
    >       --apiserver-count=1 \
    >       --bind-address=0.0.0.0 \
    >       --cert-dir=/srv/kubernetes \
    >       --client-ca-file=/srv/kubernetes/ca.crt \
    >       --enable-swagger-ui=false \
    >       --etcd-servers=http://10.64.33.81:2379 \
    >       --insecure-bind-address=127.0.0.1 \
    >       --insecure-port=8080 \
    >       --master-service-namespace=default \
    >       --runtime-config=api/all=true \
    >       --secure-port=6443 \
    >       --service-cluster-ip-range=10.123.240.0/20 \
    >       --tls-cert-file=/srv/kubernetes/server.crt \
    >       --tls-private-key-file=/srv/kubernetes/server.key \
    >       --v=2"; sudo /opt/kubernetes/server/bin/kube-apiserver $KUBE_APISERVER_OPTS

First start

    [vagrant@localhost ~]$ sudo systemctl daemon-reload

    [vagrant@localhost ~]$ sudo systemctl start kube-apiserver.service

Validation

    [vagrant@localhost ~]$ sudo systemctl -l status kube-apiserver.service
    鈼▒ kube-apiserver.service - Kubernetes API server
       Loaded: loaded (/etc/systemd/system/kube-apiserver.service; enabled; vendor preset: disabled)
       Active: active (running) since Thu 2016-11-10 22:43:54 UTC; 19s ago
         Docs: https://github.com/kubernetes/kubernetes
     Main PID: 5842 (kube-apiserver)
       CGroup: /system.slice/kube-apiserver.service
               鈹斺攢5842 /opt/kubernetes/1.3.10/kube-apiserver --admission-control=AlwaysAdmit --advertise-address=10.64.33.81 --allow-privileged=true --apiserver-count=1 --bind-address=0.0.0.0 --cert-dir=/srv/kubernetes --client-ca-file=/srv/kubernetes/ca.crt --enable-swagger-ui=false --etcd-servers=http://10.64.33.81:2379 --insecure-bind-address=127.0.0.1 --insecure-port=8080 --master-service-namespace=default --runtime-config=api/all=true --secure-port=6443 --service-cluster-ip-range=10.123.240.0/20 --tls-cert-file=/srv/kubernetes/server.cert --tls-private-key-file=/srv/kubernetes/server.key --v=2

    Nov 10 22:43:54 localhost.localdomain systemd[1]: Starting Kubernetes API server...
    Nov 10 22:43:54 localhost.localdomain kube-apiserver[5842]: I1110 22:43:54.411559    5842 genericapiserver.go:606] Will report 10.64.33.81 as public IP address.
    Nov 10 22:43:54 localhost.localdomain kube-apiserver[5842]: I1110 22:43:54.412884    5842 genericapiserver.go:288] Node port range unspecified. Defaulting to 30000-32767.
    Nov 10 22:43:54 localhost.localdomain kube-apiserver[5842]: [restful] 2016/11/10 22:43:54 log.go:30: [restful/swagger] listing is available at https://10.64.33.81:6443/swaggerapi/
    Nov 10 22:43:54 localhost.localdomain kube-apiserver[5842]: [restful] 2016/11/10 22:43:54 log.go:30: [restful/swagger] https://10.64.33.81:6443/swaggerui/ is mapped to folder /swagger-ui/
    Nov 10 22:43:54 localhost.localdomain kube-apiserver[5842]: I1110 22:43:54.505181    5842 genericapiserver.go:690] Serving securely on 0.0.0.0:6443
    Nov 10 22:43:54 localhost.localdomain kube-apiserver[5842]: I1110 22:43:54.505191    5842 genericapiserver.go:734] Serving insecurely on 127.0.0.1:8080
    Nov 10 22:43:54 localhost.localdomain systemd[1]: Started Kubernetes API server.

    [vagrant@localhost ~]$ sudo journalctl --follow --no-pager --pager-end --no-tail -u kube-apiserver.service

    [vagrant@localhost ~]$ sudo tail -100 /var/log/messages

Deep dive into kube-apiserver

    [vagrant@localhost ~]$ etcdctl ls
    /registry
    [vagrant@localhost ~]$ etcdctl ls /registry
    /registry/namespaces
    /registry/services
    /registry/ranges
    [vagrant@localhost ~]$ etcdctl ls /registry/ranges
    /registry/ranges/servicenodeports
    /registry/ranges/serviceips
    [vagrant@localhost ~]$ etcdctl get /registry/ranges/serviceips
    {"kind":"RangeAllocation","apiVersion":"v1","metadata":{"creationTimestamp":null},"range":"10.123.240.0/20","data":"AQ=="}

    [vagrant@localhost ~]$ curl http://127.0.0.1:8080/
    {
      "paths": [
        "/api",
        "/api/v1",
        "/apis",
        "/apis/apps",
        "/apis/apps/v1alpha1",
        "/apis/autoscaling",
        "/apis/autoscaling/v1",
        "/apis/batch",
        "/apis/batch/v1",
        "/apis/batch/v2alpha1",
        "/apis/extensions",
        "/apis/extensions/v1beta1",
        "/apis/policy",
        "/apis/policy/v1alpha1",
        "/apis/rbac.authorization.k8s.io",
        "/apis/rbac.authorization.k8s.io/v1alpha1",
        "/healthz",
        "/healthz/ping",
        "/logs/",
        "/metrics",
        "/swaggerapi/",
        "/ui/",
        "/version"
      ]

      [vagrant@localhost ~]$ sudo curl --cacert /srv/kubernetes/ca.crt --cert /srv/kuernetes/kubecfg.crt --key /srv/kubernetes/kubecfg.key https://10.64.33.81:6443/api
      {
        "kind": "APIVersions",
        "versions": [
          "v1"
        ],
        "serverAddressByClientCIDRs": [
          {
            "clientCIDR": "0.0.0.0/0",
            "serverAddress": "10.64.33.81:6443"
          }
        ]
      }

Command *kubectl*

    [vagrant@localhost ~]$ kubectl config set-cluster kube --server=https://10.64.33.81:6443
    cluster "kube" set.

    [vagrant@localhost ~]$ encoded=$(sudo base64 -w0 /srv/kubernetes/ca.crt); kubectl config set clusters.kube.certificate-authority-data "$encoded"
    property "clusters.kube.certificate-authority-data" set.

    [vagrant@localhost ~]$ kubectl config set-credentials admin
    user "admin" set.

    [vagrant@localhost ~]$ encoded=$(sudo base64 -w0 /srv/kubernetes/kubecfg.crt); kubectl config set users.admin.client-certificate-data "$encoded"
    property "users.admin.client-certificate-data" set.

    [vagrant@localhost ~]$ encoded=$(sudo base64 -w0 /srv/kubernetes/kubecfg.key); kubectl config set users.admin.client-key-data "$encoded"
    property "users.admin.client-key-data" set.

    [vagrant@localhost ~]$ kubectl config set-context kube-admin --cluster=kube --user=admin
    context "kube-admin" set.

    [vagrant@localhost ~]$ kubectl config use-context kube-admin
    switched to context "kube-admin".

    [vagrant@localhost ~]$ cat .kube/config

    [vagrant@localhost ~]$ kubectl version
    Client Version: version.Info{Major:"1", Minor:"3+", GitVersion:"v1.3.10-dirty", GitCommit:"c3e367ec9eae7338ac4e2a57f293634891319b7c", GitTreeState:"dirty", BuildDate:"2016-11-09T15:11:23Z", GoVersion:"go1.6.2", Compiler:"gc", Platform:"linux/amd64"}
    Server Version: version.Info{Major:"1", Minor:"3+", GitVersion:"v1.3.10-dirty", GitCommit:"c3e367ec9eae7338ac4e2a57f293634891319b7c", GitTreeState:"dirty", BuildDate:"2016-11-09T15:11:23Z", GoVersion:"go1.6.2", Compiler:"gc", Platform:"linux/amd64"}

    [vagrant@localhost ~]$ kubectl api-versions
    apps/v1alpha1
    autoscaling/v1
    batch/v1
    batch/v2alpha1
    extensions/v1beta1
    policy/v1alpha1
    rbac.authorization.k8s.io/v1alpha1
    v1

* The kube-controller-manager

Service file

    [vagrant@localhost ~]$ vi kubernetes/1.3.10/centos/systemd/system/kube-controller-manager.service
    [Unit]
    Description=Kubernetes controller manager
    Documentation=https://github.com/kubernetes/kubernetes
    # After=
    # Requires=
    # Wants=
    # Conflicts=openshift-master.service

    [Service]
    # Type=notify
    # NotifyAccess=none
    Type=simple
    User=root

    Environment=KUBERNETES_BUILD_VERSION=1.3.10
    EnvironmentFile=/etc/sysconfig/kube-controller-manager
    WorkingDirectory=/opt/kubernetes

    ExecStart=/opt/kubernetes/server/bin/kube-controller-manager $KUBE_CONTROLLER_MANAGER_OPTS
    # ExecStart=/bin/bash -c "/opt/kubernetes/${KUBERNETES_BUILD_VERSION}/centos/bin/kube-controller-manager ${KUBE_CONTROLLER_MANAGER_OPTS}"

    # Restart=always
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target

Opts file

    [vagrant@localhost ~]$ vi kubernetes/1.3.10/centos/systemd/conf/kube-controller-manager
    # KUBE_CONTROLLER_MANAGER_VERSION=1.3.10

    # ADDRESS_LISTEN=10.64.33.81

    # --cloud-config="": The path to the cloud provider configuration file

    # --cloud-provider="": The provider for cloud services

    # MASTER_ADDRESS=https://10.64.33.81:6443

    # --service-cluster-ip-range=<nil>: A CIDR notation IP range
    # Same as GKE, cluster CIDR: 10.120.0.0/14, service CIDR: 10.123.240.0/20
    # CLUSTER_CIDR=10.120.0.0/14
    # SERVICE_CIDR=10.123.240.0/20
    # CLUSTER_MASTER=10.123.240.1
    # CLUSTER_DNS=10.123.240.10

    KUBE_CONTROLLER_MANAGER_OPTS="--address=10.64.33.81 \
      --cluster-cidr=10.120.0.0/14 \
      --cluster-name=kubernetes \
      --enable-garbage-collector=false \
      --kubeconfig=/srv/kubernetes/kubeconfig \
      --master=https://10.64.33.81:6443 \
      --port=10252 \
      --root-ca-file=/srv/kubernetes/ca.crt \
      --service-account-private-key-file=/srv/kubernetes/server.key \
      --service-cluster-ip-range=10.123.240.0/20 \
      --v=2"

Manually install

    [vagrant@localhost ~]$ sudo cp .kube/config /srv/kubernetes/kubeconfig

    [vagrant@localhost ~]$ sudo cp kubernetes/1.3.10/centos/systemd/conf/kube-controller-manager /etc/sysconfig/

    [vagrant@localhost ~]$ sudo cp kubernetes/1.3.10/centos/systemd/system/kube-controller-manager.service /etc/systemd/system

    [vagrant@localhost ~]$ sudo systemctl enable kube-controller-manager.service    
    Created symlink from /etc/systemd/system/multi-user.target.wants/kube-controller-manager.service to /etc/systemd/system/kube-controller-manager.service.

Experimental run

    [vagrant@localhost ~]$ KUBE_CTLMGR_OPTS="--address=0.0.0.0 \
    >       --cluster-cidr=10.120.0.0/14 \
    >       --cluster-name=kubernetes \
    >       --enable-garbage-collector=false \
    >       --kubeconfig=/srv/kubernetes/kubeconfig \
    >       --master=https://10.64.33.81:6443 \
    >       --port=10252 \
    >       --root-ca-file=/srv/kubernetes/ca.crt \
    >       --service-account-private-key-file=/srv/kubernetes/server.key \
    >       --service-cluster-ip-range=10.123.240.0/20 \
    >       --v=2"; sudo /opt/kubernetes/server/bin/kube-controller-manager $KUBE_CTLMGR_OPTS

First start

    [vagrant@localhost ~]$ sudo systemctl daemon-reload

    [vagrant@localhost ~]$ sudo systemctl start kube-controller-manager.service

Validation

    [vagrant@localhost ~]$ sudo systemctl -l status kube-controller-manager.service
    鈼▒ kube-controller-manager.service - Kubernetes controller manager
       Loaded: loaded (/etc/systemd/system/kube-controller-manager.service; enabled; vendor preset: disabled)
       Active: active (running) since Sat 2016-11-12 16:15:45 UTC; 9s ago
         Docs: https://github.com/kubernetes/kubernetes
     Main PID: 22499 (kube-controller)
       CGroup: /system.slice/kube-controller-manager.service
               鈹斺攢22499 /opt/kubernetes/server/bin/kube-controller-manager --address=10.64.33.81 --cluster-cidr=10.120.0.0/14 --cluster-name=kubernetes --enable-garbage-collector=false --kubeconfig=/srv/kubernetes/kubeconfig --master=https://10.64.33.81:6443 --port=10252 --root-ca-file=/srv/kubernetes/ca.crt --service-account-private-key-file=/srv/kubernetes/server.key --service-cluster-ip-range=10.123.240.0/20 --v=2

    Nov 12 16:15:45 localhost.localdomain kube-controller-manager[22499]: I1112 16:15:45.722320   22499 plugins.go:340] Loaded volume plugin "kubernetes.io/aws-ebs"
    Nov 12 16:15:45 localhost.localdomain kube-controller-manager[22499]: I1112 16:15:45.722334   22499 plugins.go:340] Loaded volume plugin "kubernetes.io/gce-pd"
    Nov 12 16:15:45 localhost.localdomain kube-controller-manager[22499]: I1112 16:15:45.722340   22499 plugins.go:340] Loaded volume plugin "kubernetes.io/cinder"
    Nov 12 16:15:45 localhost.localdomain kube-controller-manager[22499]: I1112 16:15:45.722346   22499 plugins.go:340] Loaded volume plugin "kubernetes.io/vsphere-volume"
    Nov 12 16:15:45 localhost.localdomain kube-controller-manager[22499]: E1112 16:15:45.723734   22499 util.go:45] Metric for serviceaccount_controller already registered
    Nov 12 16:15:45 localhost.localdomain kube-controller-manager[22499]: I1112 16:15:45.724525   22499 attach_detach_controller.go:191] Starting Attach Detach Controller
    Nov 12 16:15:45 localhost.localdomain kube-controller-manager[22499]: W1112 16:15:45.725101   22499 request.go:347] Field selector: v1 - serviceaccounts - metadata.name - default: need to check if this is versioned correctly.
    Nov 12 16:15:45 localhost.localdomain kube-controller-manager[22499]: I1112 16:15:45.730257   22499 endpoints_controller.go:322] Waiting for pods controller to sync, requeuing service default/kubernetes
    Nov 12 16:15:45 localhost.localdomain kube-controller-manager[22499]: W1112 16:15:45.799771   22499 request.go:347] Field selector: v1 - serviceaccounts - metadata.name - default: need to check if this is versioned correctly.
    Nov 12 16:15:45 localhost.localdomain kube-controller-manager[22499]: I1112 16:15:45.848602   22499 endpoints_controller.go:322] Waiting for pods controller to sync, requeuing service default/kubernetes
    [vagrant@localhost ~]$

    [vagrant@localhost ~]$ sudo journalctl --no-pager --no-tail -u kube-controller-manager.service

    [vagrant@localhost ~]$ sudo tail -100 /var/log/messages

Investigate logs

    E1112 03:26:22.687235   11466 controllermanager.go:253] Failed to start service controller: ServiceController should not be run without a cloudprovider.

    E1112 03:26:22.690562   11466 util.go:45] Metric for replenishment_controller already registered

* The kube-scheduler

Service file

    [vagrant@localhost ~]$ vi kubernetes/centos/systemd/system/kube-scheduler.service
    [Unit]
    Description=Kubernetes scheduler
    Documentation=https://github.com/kubernetes/kubernetes
    # After=
    # Requires=
    # Wants=
    # Conflicts=openshift-master.service

    [Service]
    # Type=notify
    # NotifyAccess=main
    Type=simple
    User=root

    Environment=KUBE_BUILD_VERSION=1.3.10
    EnvironmentFile=/etc/sysconfig/kube-scheduler
    WorkingDirectory=/opt/kubernetes

    ExecStart=/opt/kubernetes/server/bin/kube-scheduler $KUBE_SCHEDULER_OPTS
    # ExecStart=/bin/bash -c "/opt/kubernetes/${KUBERNETES_BUILD_VERSION}/centos/bin/kube-scheduler ${KUBE_SCHEDULER_OPTS}"

    # Restart=always
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target

Opts file

    [vagrant@localhost ~]$ vi kubernetes/1.3.10/centos/systemd/conf/kube-scheduler
    # KUBE_SCHEDULER_VERSION=1.3.10

    # ADDRESS_LISTEN=10.64.33.81
    # MASTER_ADDRESS=https://10.64.33.81:6443

    # --cloud-config="": The path to the cloud provider configuration file
    # --cloud-provider="": The provider for cloud services

    # --service-cluster-ip-range=<nil>: A CIDR notation IP range
    # Same as GKE, cluster CIDR: 10.120.0.0/14, service CIDR: 10.123.240.0/20
    # CLUSTER_CIDR=10.120.0.0/14
    # SERVICE_CIDR=10.123.240.0/20
    # CLUSTER_MASTER=10.123.240.1
    # CLUSTER_DNS=10.123.240.10

    KUBE_SCHEDULER_OPTS="--address=10.64.33.81 \
      --algorithm-provider=DefaultProvider \
      --kubeconfig=/srv/kubernetes/kubeconfig \
      --master=https://10.64.33.81:6443 \
      --port=10251 \
      --scheduler-name=default-scheduler \
      --v=2"

Manually install

    [vagrant@localhost ~]$ sudo cp kubernetes/1.3.10/centos/systemd/conf/kube-scheduler /etc/systemd/

    [vagrant@localhost ~]$ sudo cp kubernetes/1.3.10/centos/systemd/system/kube-scheduler.service /etc/systemd/system

    [vagrant@localhost ~]$ sudo systemctl enable kube-scheduler
    Created symlink from /etc/systemd/system/multi-user.target.wants/kube-scheduler.service to /etc/systemd/system/kube-scheduler.service.

Experimental run

    [vagrant@localhost ~]$ KUBE_SCHEDULER_OPTS="--address=0.0.0.0 \
    >       --algorithm-provider=DefaultProvider \
    >       --kubeconfig=/srv/kubernetes/kubeconfig \
    >       --master=https://10.64.33.81:6443 \
    >       --port=10251 \
    >       --scheduler-name=default-scheduler \
    >       --v=2"; sudo /opt/kubernetes/server/bin/kube-scheduler $KUBE_SCHEDULER_OPTS

First start

    [vagrant@localhost ~]$ sudo systemctl daemon-reload

    [vagrant@localhost ~]$ sudo systemctl start kube-scheduler.service

Validation

    [vagrant@localhost ~]$ sudo systemctl -l status kube-scheduler.service
    鈼▒ kube-scheduler.service - Kubernetes scheduler
       Loaded: loaded (/etc/systemd/system/kube-scheduler.service; enabled; vendor preset: disabled)
       Active: active (running) since Sat 2016-11-12 16:34:52 UTC; 9s ago
         Docs: https://github.com/kubernetes/kubernetes
     Main PID: 23697 (kube-scheduler)
       CGroup: /system.slice/kube-scheduler.service
               鈹斺攢23697 /opt/kubernetes/server/bin/kube-scheduler --address=10.64.33.81 --algorithm-provider=DefaultProvider --kubeconfig=/srv/kubernetes/kubeconfig --master=https://10.64.33.81:6443 --port=10251 --scheduler-name=default-scheduler --v=2

    Nov 12 16:34:52 localhost.localdomain systemd[1]: Started Kubernetes scheduler.
    Nov 12 16:34:52 localhost.localdomain systemd[1]: Starting Kubernetes scheduler...
    Nov 12 16:34:52 localhost.localdomain kube-scheduler[23697]: I1112 16:34:52.405887   23697 factory.go:255] Creating scheduler from algorithm provider 'DefaultProvider'
    Nov 12 16:34:52 localhost.localdomain kube-scheduler[23697]: I1112 16:34:52.405941   23697 factory.go:301] creating scheduler with fit predicates 'map[NoVolumeZoneConflict:{} MaxEBSVolumeCount:{} MaxGCEPDVolumeCount:{} GeneralPredicates:{} PodToleratesNodeTaints:{} CheckNodeMemoryPressure:{} NoDiskConflict:{}]' and priority functions 'map[SelectorSpreadPriority:{} NodeAffinityPriority:{} TaintTolerationPriority:{} LeastRequestedPriority:{} BalancedResourceAllocation:{}]

    [vagrant@localhost ~]$ sudo journalctl --follow --no-pager --pager-end --no-tail -u kube-scheduler.service

    [vagrant@localhost ~]$ sudo cat /var/log/messages | grep kube-scheduler

* The kubelet

Service file

    [vagrant@localhost ~]$ vi kubernetes/1.3.10/centos/systemd/system/kubelet.service
    [Unit]
    Description=Kubernetes container mgmt daemon - kubelet
    Documentation=https://github.com/kubernetes/kubernetes
    After=docker.service
    Requires=docker.service
    # Wants=
    Conflicts=openshift-node.service

    [Service]
    # Type=notify
    # NotifyAccess=all
    Type=simple
    User=root

    Environment=KUBERNETES_BUILD_VERSION=1.3.10
    EnvironmentFile=/etc/sysconfig/kubelet
    WorkingDirectory=/opt/kubernetes

    ExecStartPre=/usr/bin/mkdir -p /etc/kubernetes/manifests
    ExecStartPre=/usr/bin/mkdir -p /var/log/containers

    ExecStart=/opt/kubernetes/server/bin/kubelet $KUBELET_OPTS
    # ExecStart=/bin/bash -c "/opt/kubernetes/${KUBERNETES_BUILD_VERSION}/centos/bin/kubelet ${KUBELET_OPTS}"

    # Restart=always
    Restart=on-failure
    RestartSec=10
    KillMode=process

    [Install]
    WantedBy=multi-user.target

Opts file

    [vagrant@localhost ~]$ vi kubernetes/1.3.10/centos/systemd/conf/kubelet
    # KUBELET_VERSION=1.3.10

    # --api-servers=[]: Comma separated list of Kubernetes API servers (ip:port).
    # API_SERVERS=https://10.64.33.81:6443

    # --hostname-override="": As identification instad of the actual hostname
    # HOSTNAME_OVERRIDE=10.64.33.81

    # --cert-dir="/var/run/kubernetes"
    # CERT_DIR=/srv/kubernetes

    # CONFIG_PATH=/etc/kubernetes/manifests

    # --kubeconfig="/var/lib/kubelet/kubeconfig": Path to a kubeconfig file
    # KUBECONFIG_PATH=/srv/kubernetes/kubeconfig

    # --master-service-namespace="default": The master services should be injected into pods

    # --service-cluster-ip-range=<nil>: A CIDR notation IP range
    # Same as GKE, cluster CIDR: 10.120.0.0/14, service CIDR: 10.123.240.0/20
    # CLUSTER_CIDR=10.120.0.0/14
    # SERVICE_CIDR=10.123.240.0/20
    # CLUSTER_MASTER=10.123.240.1
    # CLUSTER_DNS=10.123.240.10

    KUBELET_OPTS="--address=10.64.33.81 \
      --allow-privileged=true \
      --api-servers=https://10.64.33.81:6443 \
      --cadvisor-port=4194 \
      --cert-dir=/srv/kubernetes \
      --cluster-dns=10.123.240.10 \
      --cluster-domain=cluster.local \
      --config=/etc/kubernetes/manifests \
      --configure-cbr0=false \
      --container-runtime=docker \
      --docker=unix:///var/run/docker.sock \
      --healthz-bind-address=127.0.0.1 \
      --healthz-port=10248 \
      --hostname-override=10.64.33.81 \
      --kubeconfig=/srv/kubernetes/kubeconfig \
      --master-service-namespace=default \
      --max-open-files=1000000 \
      --max-pods=0 \
      --node-ip=10.64.33.81 \
      --non-masquerade-cidr=10.0.0.0/8 \
      --pod-infra-container-image=gcr.io/google_containers/pause-amd64:3.0 \
      --pods-per-core=0 \
      --port=10250 \
      --read-only-port=10255 \
      --root-dir=/var/lib/kubelet \
      --tls-cert-file=/srv/kubernetes/server.cert \
      --tls-private-key-file=/srv/kubernetes/server.key \
      --v=2"

Manually install

    [vagrant@localhost ~]$ sudo cp kubernetes/1.3.10/centos/systemd/conf/kubelet /etc/sysconfig/

    [vagrant@localhost ~]$ sudo cp kubernetes/1.3.10/centos/systemd/system/kubelet.service /etc/systemd/system

    [vagrant@localhost ~]$ sudo systemctl enable kubelet.service
    Created symlink from /etc/systemd/system/multi-user.target.wants/kubelet.service to /etc/systemd/system/kubelet.service.

Experimental run

    [vagrant@localhost ~]$ KUBELET_OPTS="--address=0.0.0.0 \
    >       --allow-privileged=true \
    >       --api-servers=https://10.64.33.81:6443 \
    >       --cadvisor-port=4194 \
    >       --cert-dir=/srv/kubernetes \
    >       --cluster-dns=10.123.240.10 \
    >       --cluster-domain=cluster.local \
    >       --config=/opt/kubernetes/manifests \
    >       --configure-cbr0=false \
    >       --container-runtime=docker \
    >       --docker=unix:///var/run/docker.sock \
    >       --experimental-nvidia-gpus=0 \
    >       --healthz-bind-address=127.0.0.1 \
    >       --healthz-port=10248 \
    >       --hostname-override=10.64.33.81 \
    >       --kubeconfig=/srv/kubernetes/kubeconfig \
    >       --master-service-namespace=default \
    >       --max-open-files=1000000 \
    >       --max-pods=110 \
    >       --node-ip=10.64.33.81 \
    >       --non-masquerade-cidr=10.0.0.0/8 \
    >       --pod-infra-container-image=gcr.io/google_containers/pause-amd64:3.0 \
    >       --pods-per-core=0 \
    >       --port=10250 \
    >       --root-dir=/var/lib/kubelet \
    >       --tls-cert-file=/srv/kubernetes/server.cert \
    >       --tls-private-key-file=/srv/kubernetes/server.key \
    >       --v=2"; sudo /opt/kubernetes/server/bin/kubelet $KUBELET_OPTS

First start

    [vagrant@localhost ~]$ sudo systemctl daemon-reload                             

    [vagrant@localhost ~]$ sudo systemctl start kubelet.service

Validation

    [vagrant@localhost ~]$ sudo systemctl -l status kubelet.service
    鈼▒ kubelet.service - Kubernetes container mgmt daemon - kubelet
       Loaded: loaded (/etc/systemd/system/kubelet.service; enabled; vendor preset: disabled)
       Active: active (running) since Sat 2016-11-12 15:11:08 UTC; 40min ago
         Docs: https://github.com/kubernetes/kubernetes
     Main PID: 17657 (kubelet)
       CGroup: /system.slice/kubelet.service
               鈹溾攢17657 /opt/kubernetes/server/bin/kubelet --address=10.64.33.81 --allow-privileged=true --api-servers=https://10.64.33.81:6443 --cadvisor-port=4194 --cert-dir=/srv/kubernetes --cluster-dns=10.123.240.10 --cluster-domain=cluster.local --config=/etc/kubernetes/manifests --configure-cbr0=false --container-runtime=docker --docker=unix:///var/run/docker.sock --healthz-bind-address=127.0.0.1 --healthz-port=10248 --hostname-override=10.64.33.81 --kubeconfig=/srv/kubernetes/kubeconfig --master-service-namespace=default --max-open-files=1000000 --max-pods=0 --node-ip=10.64.33.81 --non-masquerade-cidr=10.0.0.0/8 --pod-infra-container-image=gcr.io/google_containers/pause-amd64:3.0 --pods-per-core=0 --port=10250 --read-only-port=10255 --root-dir=/var/lib/kubelet --tls-cert-file=/srv/kubernetes/server.cert --tls-private-key-file=/srv/kubernetes/server.key --v=2

    Nov 12 15:46:09 localhost.localdomain kubelet[17657]: W1112 15:46:09.340116   17657 container_manager_linux.go:589] CPUAccounting not enabled for pid: 1076
    Nov 12 15:46:09 localhost.localdomain kubelet[17657]: W1112 15:46:09.340156   17657 container_manager_linux.go:592] MemoryAccounting not enabled for pid: 1076
    Nov 12 15:46:09 localhost.localdomain kubelet[17657]: I1112 15:46:09.340171   17657 container_manager_linux.go:290] Discovered runtime cgroups name: /system.slice/docker.service
    Nov 12 15:46:09 localhost.localdomain kubelet[17657]: W1112 15:46:09.340318   17657 container_manager_linux.go:589] CPUAccounting not enabled for pid: 17657
    Nov 12 15:46:09 localhost.localdomain kubelet[17657]: W1112 15:46:09.340334   17657 container_manager_linux.go:592] MemoryAccounting not enabled for pid: 17657
    Nov 12 15:51:09 localhost.localdomain kubelet[17657]: W1112 15:51:09.347243   17657 container_manager_linux.go:589] CPUAccounting not enabled for pid: 1076
    Nov 12 15:51:09 localhost.localdomain kubelet[17657]: W1112 15:51:09.347277   17657 container_manager_linux.go:592] MemoryAccounting not enabled for pid: 1076
    Nov 12 15:51:09 localhost.localdomain kubelet[17657]: I1112 15:51:09.347292   17657 container_manager_linux.go:290] Discovered runtime cgroups name: /system.slice/docker.service
    Nov 12 15:51:09 localhost.localdomain kubelet[17657]: W1112 15:51:09.354968   17657 container_manager_linux.go:589] CPUAccounting not enabled for pid: 17657
    Nov 12 15:51:09 localhost.localdomain kubelet[17657]: W1112 15:51:09.355003   17657 container_manager_linux.go:592] MemoryAccounting not enabled for pid: 17657

    [vagrant@localhost ~]$ sudo journalctl --follow --no-tail --no-pager --pager-end -u kubelet.service

    [vagrant@localhost ~]$ sudo tail -100 /var/log/messages | grep kubelet

    [vagrant@localhost ~]$ sudo journalctl -k -f

Using kubectl

    [vagrant@localhost ~]$ kubectl get nodes
    NAME          STATUS    AGE
    10.64.33.81   Ready     26m

    [vagrant@localhost ~]$ kubectl get nodes/10.64.33.81 -o yaml
    apiVersion: v1
    kind: Node
    metadata:
      annotations:
        volumes.kubernetes.io/controller-managed-attach-detach: "true"
      creationTimestamp: 2016-11-11T22:58:41Z
      labels:
        beta.kubernetes.io/arch: amd64
        beta.kubernetes.io/os: linux
        kubernetes.io/hostname: 10.64.33.81
      name: 10.64.33.81
      resourceVersion: "103"
      selfLink: /api/v1/nodes/10.64.33.81
      uid: 63c86df9-a862-11e6-8d5b-5254009fbdfd
    spec:
      externalID: 10.64.33.81
    status:
      addresses:
      - address: 10.64.33.81
        type: LegacyHostIP
      - address: 10.64.33.81
        type: InternalIP
      allocatable:
        alpha.kubernetes.io/nvidia-gpu: "0"
        cpu: "1"
        memory: 2916372Ki
        pods: "110"
      capacity:
        alpha.kubernetes.io/nvidia-gpu: "0"
        cpu: "1"
        memory: 2916372Ki
        pods: "110"
      conditions:
      - lastHeartbeatTime: 2016-11-11T23:25:59Z
        lastTransitionTime: 2016-11-11T23:22:08Z
        message: kubelet has sufficient disk space available
        reason: KubeletHasSufficientDisk
        status: "False"
        type: OutOfDisk
      - lastHeartbeatTime: 2016-11-11T23:25:59Z
        lastTransitionTime: 2016-11-11T22:58:41Z
        message: kubelet has sufficient memory available
        reason: KubeletHasSufficientMemory
        status: "False"
        type: MemoryPressure
      - lastHeartbeatTime: 2016-11-11T23:25:59Z
        lastTransitionTime: 2016-11-11T23:22:08Z
        message: kubelet is posting ready status
        reason: KubeletReady
        status: "True"
        type: Ready
      daemonEndpoints:
        kubeletEndpoint:
          Port: 10250
      nodeInfo:
        architecture: amd64
        bootID: b14335fa-f746-46c7-9517-44f1f9353cc5
        containerRuntimeVersion: docker://1.10.3
        kernelVersion: 3.10.0-327.36.1.el7.x86_64
        kubeProxyVersion: v1.3.10
        kubeletVersion: v1.3.10
        machineID: c1bde9cb97f44f9194d26b125a13dd44
        operatingSystem: linux
        osImage: CentOS Linux 7 (Core)
        systemUUID: C1BDE9CB-97F4-4F91-94D2-6B125A13DD44

Investigate logs

    E1112 03:13:18.667969    9980 kubelet.go:954] Image garbage collection failed: unable to find data for container /

    E1111 22:58:41.544101    4389 factory.go:291] devicemapper filesystem stats will not be reported: RHEL/Centos 7.x kernel version 3.10.0-366 or later is required to use thin_ls - you have "3.10.0-327.36.1.el7.x86_64"

* The kube-proxy

Service file

    [vagrant@localhost ~]$ vi kubernetes/1.3.10/centos/systemd/system/kube-proxy.service
    [Unit]
    Description=Kubernetes proxy networking
    Documentation=https://github.com/kubernetes/kubernetes
    # After=
    # Requires=
    # Wants=
    # Conflicts=openshift-node.service

    [Service]
    # Type=notify
    # NotifyAccess=none
    Type=simple
    User=root

    Environment=KUBERNETES_RELEASE_VERSION=1.3.10
    EnvironmentFile=/etc/sysconfig/kube-proxy
    WorkingDirectory=/opt/kubernetes

    ExecStart=/opt/kubernetes/server/bin/kube-proxy $KUBE_PROXY_OPTS
    # ExecStart=/bin/bash -c "/opt/kubernetes/${KUBERNETES_RELEASE_VERSION}/centos/bin/kube-proxy ${KUBE_PROXY_OPTS}"

    # Restart=always
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target

Opts file

    [vagrant@localhost ~]$ vi kubernetes/1.3.10/centos/systemd/conf/kube-proxy
    # KUBE_PROXY_VERSION=1.3.10

    # BIND_ADDRESS=10.64.33.81
    # HOSTNAME_OVERRIDE=10.64.33.81

    # --iptables-masquerade-bit=14: If using the pure iptables proxy, the bit of the fwmark space to mark packets requiring SNAT with. Must be within [0, 31]

    # KUBECONFIG_PATH=/srv/kubernetes/kubeconfig
    # MASTER_ADDRESS=https://10.64.33.81:6443

    # --service-cluster-ip-range=<nil>: A CIDR notation IP range
    # Same as GKE, cluster CIDR: 10.120.0.0/14, service CIDR: 10.123.240.0/20
    # CLUSTER_CIDR=10.120.0.0/14
    # SERVICE_CIDR=10.123.240.0/20
    # CLUSTER_MASTER=10.123.240.1
    # CLUSTER_DNS=10.123.240.10

    KUBE_PROXY_OPTS="--bind-address=10.64.33.81 \
      --cluster-cidr=10.120.0.0/14 \
      --healthz-bind-address=127.0.0.1 \
      --healthz-port=10249 \
      --hostname-override=10.64.33.81 \
      --iptables-masquerade-bit=14 \
      --kubeconfig=/srv/kubernetes/kubeconfig \
      --master=https://10.64.33.81:6443 \
      --v=2"

Manually Install

    [vagrant@localhost ~]$ sudo cp kubernetes/1.3.10/centos/systemd/conf/kube-proxy /etc/sysconfig/

    [vagrant@localhost ~]$ sudo cp kubernetes/1.3.10/centos/systemd/system/kube-proxy.service /etc/systemd/system

    [vagrant@localhost ~]$ sudo systemctl enable kube-proxy.service
    Created symlink from /etc/systemd/system/multi-user.target.wants/kube-proxy.service to /etc/systemd/system/kube-proxy.service.

Experimental Run

    [vagrant@localhost ~]$ KUBE_PROXY_OPTS="--bind-address=0.0.0.0 \
    >       --cluster-cidr=10.120.0.0/14 \
    >       --healthz-bind-address=127.0.0.1 \
    >       --healthz-port=10249 \
    >       --hostname-override=10.64.33.81 \
    >       --iptables-masquerade-bit=14 \
    >       --kubeconfig=/srv/kubernetes/kubeconfig \
    >       --master=https://10.64.33.81:6443 \
    >       --v=2"; sudo /opt/kubernetes/server/bin/kube-proxy $KUBE_PROXY_OPTS

First start

    [vagrant@localhost ~]$ sudo systemctl daemon-reload

    [vagrant@localhost ~]$ sudo systemctl start kube-proxy.service

Validation

    [vagrant@localhost ~]$ sudo systemctl -l status kube-proxy.service
    鈼▒ kube-proxy.service - Kubernetes proxy networking
       Loaded: loaded (/etc/systemd/system/kube-proxy.service; enabled; vendor preset: disabled)
       Active: active (running) since Sat 2016-11-12 15:47:53 UTC; 8min ago
         Docs: https://github.com/kubernetes/kubernetes
     Main PID: 20694 (kube-proxy)
       CGroup: /system.slice/kube-proxy.service
               鈹斺攢20694 /opt/kubernetes/server/bin/kube-proxy --bind-address=10.64.33.81 --cluster-cidr=10.120.0.0/14 --healthz-bind-address=127.0.0.1 --healthz-port=10249 --hostname-override=10.64.33.81 --iptables-masquerade-bit=14 --kubeconfig=/srv/kubernetes/kubeconfig --master=https://10.64.33.81:6443 --v=2

    Nov 12 15:47:53 localhost.localdomain kube-proxy[20694]: I1112 15:47:53.916429   20694 server.go:155] setting OOM scores is unsupported in this build
    Nov 12 15:47:53 localhost.localdomain kube-proxy[20694]: I1112 15:47:53.948369   20694 server.go:202] Using iptables Proxier.
    Nov 12 15:47:53 localhost.localdomain kube-proxy[20694]: I1112 15:47:53.949826   20694 proxier.go:216] missing br-netfilter module or unset br-nf-call-iptables; proxy may not work as intended
    Nov 12 15:47:53 localhost.localdomain kube-proxy[20694]: I1112 15:47:53.949855   20694 server.go:214] Tearing down userspace rules.
    Nov 12 15:47:53 localhost.localdomain kube-proxy[20694]: I1112 15:47:53.959059   20694 conntrack.go:40] Setting nf_conntrack_max to 65536
    Nov 12 15:47:53 localhost.localdomain kube-proxy[20694]: I1112 15:47:53.959696   20694 conntrack.go:57] Setting conntrack hashsize to 16384
    Nov 12 15:47:53 localhost.localdomain kube-proxy[20694]: I1112 15:47:53.959785   20694 conntrack.go:62] Setting nf_conntrack_tcp_timeout_established to 86400
    Nov 12 15:47:53 localhost.localdomain kube-proxy[20694]: I1112 15:47:53.961241   20694 proxier.go:440] Adding new service "default/kubernetes:https" at 10.123.240.1:443/TCP
    Nov 12 15:47:53 localhost.localdomain kube-proxy[20694]: I1112 15:47:53.961333   20694 proxier.go:674] Not syncing iptables until Services and Endpoints have been received from master
    Nov 12 15:47:53 localhost.localdomain kube-proxy[20694]: I1112 15:47:53.977742   20694 proxier.go:516] Setting endpoints for "default/kubernetes:https" to [10.64.33.81:6443]

    [vagrant@localhost ~]$ sudo journalctl --follow --no-tail --no-pager --pager-end -u kube-proxy.service

Using kubectl

    [vagrant@localhost ~]$ kubectl get endpoints,services
    NAME         ENDPOINTS          AGE
    kubernetes   10.64.33.81:6443   1d
    NAME         CLUSTER-IP         EXTERNAL-IP   PORT(S)   AGE
    kubernetes   10.123.240.1       <none>        443/TCP   1d

### Run kub-apiserver, kube-controller-manager, kube-scheduler, kube-proxy as PODs

Reference

    https://github.com/coreos/coreos-kubernetes/blob/v0.4.0/multi-node/generic/controller-install.sh

Pod image
----------

Save local pulled

Load from image archive

A *gofileserver* HTTP archives

    tangf@DESKTOP-H68OQDV /cygdrive/g/2015-12-19-repository/99-mirror
    $ gofileserver.exe
    Listening at  :48080

    [vagrant@localhost ~]$ curl -L http://192.168.1.100:48080/
    <pre>
    <a href="centos/">centos/</a>
    <a href="gcr.io%252Fgoogle_containers/">gcr.io%2Fgoogle_containers/</a>
    </pre>

    [vagrant@localhost ~]$ wget -r http://192.168.1.100:48080/gcr.io%252Fgoogle_containers/  
    --2016-11-12 18:55:01--  http://192.168.1.100:48080/gcr.io%252Fgoogle_containers/gcr.io%252Fgoogle_containers%252Fkubernetes-dashboard-amd64%253Av1.0.1.tar
    Reusing existing connection to 192.168.1.100:48080.
    HTTP request sent, awaiting response... 200 OK
    Length: 44133888 (42M) [application/x-tar]
    Saving to: 鈥▒192.168.1.100:48080/gcr.io%2Fgoogle_containers/gcr.io%2Fgoogle_containers%2Fkubernetes-dashboard-amd64%3Av1.0.1.tar鈥▒

    30% [==========>                            ] 13,531,220  33.2KB/s  eta 10m 40s



Kubernetes addons
--------------------

* Start kube-dns


Install worker node
--------------------

Same as preview steps, only validate to:

* copy `CA and certs`

* install `kubelet`

* install `kube-proxy`

* load `POD image`

* Validation

After `docker` and `flanneld` installed

Copy `CA and certs`

    [tangfx@localhost ~]$ sudo cp .pki/kubernetes/* /etc/kubernetes/cacerts

Install `kubelet`

    [tangfx@localhost ~]$ vi kubernetes/1.3.1/fedora23/systemd/system/kubelet.service
    [Unit]
    Description=Kubernetes container mgmt daemon - kubelet
    Documentation=https://github.com/kubernetes/kubernetes
    After=docker.service
    Requires=docker.service
    # Wants=
    Conflicts=openshift-node.service

    [Service]
    Type=simple
    # Type=notify
    # NotifyAccess=all
    User=root

    WorkingDirectory=/opt/kubernetes

    Environment=KUBERNETES_RELEASE_VERSION=1.3.1
    EnvironmentFile=/etc/sysconfig/kubelet

    ExecStartPre=/usr/bin/mkdir -p /etc/kubernetes/manifests
    ExecStartPre=/usr/bin/mkdir -p /var/log/containers

    # ExecStart=/opt/kubernetes/server/bin/kubelet $KUBELET_OPTS
    ExecStart=/bin/bash -c "/opt/kubernetes/${KUBERNETES_RELEASE_VERSION}/fedora23/bin/kubelet ${KUBELET_OPTS}"

    # Restart=always
    Restart=on-failure
    RestartSec=10
    KillMode=process

    [Install]
    WantedBy=multi-user.target

    [tangfx@localhost ~]$ vi kubernetes/1.3.1/fedora23/systemd/conf/kubelet
    # KUBELET_VERSION=1.3.10

    # --api-servers=[]: Comma separated list of Kubernetes API servers (ip:port).
    # API_SERVERS=https://10.64.33.81:6443

    # --cert-dir="/var/run/kubernetes"
    # CERT_DIR=/home/tangfx/.pki/kubernetes

    # --hostname-override="": As identification instad of the actual hostname
    # HOSTNAME_OVERRIDE=10.64.33.90

    # --kubeconfig="/var/lib/kubelet/kubeconfig": Path to a kubeconfig file
    # KUBECONFIG_PATH=/home/tangfx/.pki/kubernetes/kubeconfig

    # --master-service-namespace="default": The master services should be injected into pods

    # --service-cluster-ip-range=<nil>: A CIDR notation IP range
    # Same as GKE, cluster CIDR: 10.120.0.0/14, service CIDR: 10.123.240.0/20
    # SERVICE_CIDR=10.123.240.0/20
    # SERVICE_MASTER=10.123.240.1
    # CLUSTER_DNS=10.123.240.10

    # TLS_SERVER_CERT=/home/tangfx/.pki/kubernetes/server.cert
    # TLS_SERVER_KEY=/home/tangfx/.pki/kubernetes/server.key

    #KUBELET_OPTS="--address=10.64.33.90 \
    #  --allow-privileged=true \
    #  --api-servers=https://10.64.33.81:6443 \
    #  --cadvisor-port=4194 \
    #  --cert-dir=/home/tangfx/.pki/kubernetes \
    #  --cluster-dns=10.123.240.10 \
    #  --cluster-domain=cluster.local \
    #  --config=/etc/kubernetes/manifests \
    #  --configure-cbr0=false \
    #  --container-runtime=docker \
    #  --docker=unix:///var/run/docker.sock \
    #  --healthz-bind-address=127.0.0.1 \
    #  --healthz-port=10248 \
    #  --hostname-override=10.64.33.90 \
    #  --kubeconfig=/home/tangfx/.pki/kubernetes/kubeconfig \
    #  --master-service-namespace=default \
    #  --max-open-files=1000000 \
    #  --max-pods=110 \
    #  --node-ip=10.64.33.90 \
    #  --non-masquerade-cidr=10.0.0.0/8 \
    #  --pod-infra-container-image=gcr.io/google_containers/pause-amd64:3.0 \
    #  --pods-per-core=0 \
    #  --port=10250 \
    #  --read-only-port=10255 \
    #  --root-dir=/var/lib/kubelet \
    #  --tls-cert-file=/home/tangfx/.pki/kubernetes/server.cert \
    #  --tls-private-key-file=/home/tangfx/.pki/kubernetes/server.key \
    #  --v=2"

    KUBELET_OPTS="--address=10.64.33.90 \
      --allow-privileged=false \
      --api-servers=https://10.64.33.81:6443 \
      --cluster-dns=10.123.240.10 \
      --cluster-domain=cluster.local \
      --config=/etc/kubernetes/manifests \
      --hostname-override=10.64.33.90 \
      --kubeconfig=/home/tangfx/.pki/kubernetes/kubeconfig \
      --node-ip=10.64.33.90 \
      --v=2"

    [tangfx@localhost ~]$ sudo cp kubernetes/1.3.1/fedora23/systemd/system/kubelet.service /etc/systemd/system/

    [tangfx@localhost ~]$ sudo cp kubernetes/1.3.1/fedora23/systemd/conf/kubelet /etc/sysconfig/

    [tangfx@localhost ~]$ sudo systemctl enable kubelet.service

    [tangfx@localhost ~]$ sudo systemctl daemon-reload

    [tangfx@localhost ~]$ sudo systemctl start kubelet.service

    [tangfx@localhost ~]$ sudo systemctl -l status kubelet.service                  
    鈼▒ kubelet.service - Kubernetes container mgmt daemon - kubelet
       Loaded: loaded (/etc/systemd/system/kubelet.service; enabled; vendor preset: disabled)
       Active: active (running) since Sat 2016-11-12 23:39:31 CST; 3h 52min ago
         Docs: https://github.com/kubernetes/kubernetes
      Process: 10194 ExecStartPre=/usr/bin/mkdir -p /var/log/containers (code=exited, status=0/SUCCESS)
      Process: 10192 ExecStartPre=/usr/bin/mkdir -p /etc/kubernetes/manifests (code=exited, status=0/SUCCESS)
     Main PID: 10196 (kubelet)
       CGroup: /system.slice/kubelet.service
               鈹溾攢10196 /opt/kubernetes/1.3.1/fedora23/bin/kubelet --address=10.64.33.90 --allow-privileged=false --api-servers=https://10.64.33.81:6443 --cluster-dns=10.123.240.10 --cluster-domain=cluster.local --config=/etc/kubernetes/manifests --hostname-override=10.64.33.90 --kubeconfig=/home/tangfx/.pki/kubernetes/kubeconfig --node-ip=10.64.33.90 --v=2
               鈹斺攢10215 journalctl -k -f

    Nov 13 03:29:32 localhost.localdomain bash[10196]: W1113 03:29:32.995284   10196 container_manager_linux.go:573] CPUAccounting not enabled for pid: 10196
    Nov 13 03:29:32 localhost.localdomain bash[10196]: W1113 03:29:32.995567   10196 container_manager_linux.go:576] MemoryAccounting not enabled for pid: 10196
    Nov 13 03:29:40 localhost.localdomain bash[10196]: I1113 03:29:40.967632   10196 thin_pool_watcher.go:126] reserving metadata snapshot for thin-pool docker-253:0-67108972-pool
    Nov 13 03:29:56 localhost.localdomain bash[10196]: I1113 03:29:56.082104   10196 thin_pool_watcher.go:126] reserving metadata snapshot for thin-pool docker-253:0-67108972-pool
    Nov 13 03:30:11 localhost.localdomain bash[10196]: I1113 03:30:11.241585   10196 thin_pool_watcher.go:126] reserving metadata snapshot for thin-pool docker-253:0-67108972-pool
    Nov 13 03:30:26 localhost.localdomain bash[10196]: I1113 03:30:26.333483   10196 thin_pool_watcher.go:126] reserving metadata snapshot for thin-pool docker-253:0-67108972-pool
    Nov 13 03:30:41 localhost.localdomain bash[10196]: I1113 03:30:41.507199   10196 thin_pool_watcher.go:126] reserving metadata snapshot for thin-pool docker-253:0-67108972-pool
    Nov 13 03:30:56 localhost.localdomain bash[10196]: I1113 03:30:56.594531   10196 thin_pool_watcher.go:126] reserving metadata snapshot for thin-pool docker-253:0-67108972-pool
    Nov 13 03:31:11 localhost.localdomain bash[10196]: I1113 03:31:11.687486   10196 thin_pool_watcher.go:126] reserving metadata snapshot for thin-pool docker-253:0-67108972-pool
    Nov 13 03:31:26 localhost.localdomain bash[10196]: I1113 03:31:26.816454   10196 thin_pool_watcher.go:126] reserving metadata snapshot for thin-pool docker-253:0-67108972-pool

    [tangfx@localhost ~]$ sudo journalctl --no-tail --no-pager --pager-end --lines=10 --unit=kubelet.service

Install `kube-proxy`

    [tangfx@localhost ~]$ vi kubernetes/1.3.1/fedora23/systemd/system/kube-proxy.service
    [Unit]
    Description=Kubernetes proxy networking
    Documentation=https://github.com/kubernetes/kubernetes
    # After=
    # Requires=
    # Wants=
    # Conflicts=openshift-node.service

    [Service]
    # Type=notify
    # NotifyAccess=none
    Type=simple
    User=root

    Environment=KUBERNETES_RELEASE_VERSION=1.3.1
    EnvironmentFile=/etc/sysconfig/kube-proxy
    WorkingDirectory=/opt/kubernetes

    # ExecStart=/opt/kubernetes/server/bin/kube-proxy $KUBE_PROXY_OPTS
    ExecStart=/bin/bash -c "/opt/kubernetes/${KUBERNETES_RELEASE_VERSION}/fedora23/bin/kube-proxy ${KUBE_PROXY_OPTS}"

    # Restart=always
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target    

    [tangfx@localhost ~]$ vi kubernetes/1.3.1/fedora23/systemd/conf/kube-proxy
    # KUBE_PROXY_VERSION=1.3.10

    # BIND_ADDRESS=10.64.33.90
    # HOSTNAME_OVERRIDE=10.64.33.90

    # --iptables-masquerade-bit=14: If using the pure iptables proxy, the bit of the fwmark space to mark packets requiring SNAT with. Must be within [0, 31]

    # KUBECONFIG_PATH=/home/tangfx/.pki/kubernetes/kubeconfig

    # MASTER_ADDRESS=https://10.64.33.81:6443

    # --service-cluster-ip-range=<nil>: A CIDR notation IP range
    # Same as GKE, cluster CIDR: 10.120.0.0/14, service CIDR: 10.123.240.0/20
    # CLUSTER_CIDR=10.120.0.0/14
    # SERVICE_CIDR=10.123.240.0/20
    # CLUSTER_MASTER=10.123.240.1
    # SERVICE_DNS=10.123.240.10

    KUBE_PROXY_OPTS="--bind-address=10.64.33.90 \
      --cluster-cidr=10.120.0.0/14 \
      --healthz-bind-address=127.0.0.1 \
      --healthz-port=10249 \
      --hostname-override=10.64.33.90 \
      --iptables-masquerade-bit=14 \
      --kubeconfig=/home/tangfx/.pki/kubernetes/kubeconfig \
      --master=https://10.64.33.81:6443 \
      --v=2"

    [tangfx@localhost ~]$ sudo cp kubernetes/1.3.1/fedora23/systemd/system/kube-proxy.service /etc/systemd/system/

    [tangfx@localhost ~]$ sudo cp kubernetes/1.3.1/fedora23/systemd/conf/kube-proxy /etc/sysconfig/

    [tangfx@localhost ~]$ sudo systemctl enable kube-proxy.service

    [tangfx@localhost ~]$ sudo systemctl daemon-reload

    [tangfx@localhost ~]$ sudo systemctl start kube-proxy.service

    [tangfx@localhost ~]$ sudo systemctl -l status kube-proxy.service
    鈼▒ kube-proxy.service - Kubernetes proxy networking
       Loaded: loaded (/etc/systemd/system/kube-proxy.service; enabled; vendor preset: disabled)
       Active: active (running) since Sat 2016-11-12 23:39:58 CST; 3h 42min ago
         Docs: https://github.com/kubernetes/kubernetes
     Main PID: 10262 (kube-proxy)
       CGroup: /system.slice/kube-proxy.service
               鈹斺攢10262 /opt/kubernetes/1.3.1/fedora23/bin/kube-proxy --bind-address=10.64.33.90 --cluster-cidr=10.120.0.0/14 --healthz-bind-address=127.0.0.1 --healthz-port=10249 --hostname-override=10.64.33.90 --iptables-masquerade-bit=14 --kubeconfig=/home/tangfx/.pki/kubernetes/kubeconfig --master=https://10.64.33.81:6443 --v=2

    Nov 12 23:39:58 localhost.localdomain bash[10262]: I1112 23:39:58.626897   10262 conntrack.go:36] Setting nf_conntrack_max to 262144
    Nov 12 23:39:58 localhost.localdomain bash[10262]: I1112 23:39:58.627147   10262 conntrack.go:41] Setting conntrack hashsize to 65536
    Nov 12 23:39:58 localhost.localdomain bash[10262]: I1112 23:39:58.627751   10262 conntrack.go:46] Setting nf_conntrack_tcp_timeout_established to 86400
    Nov 12 23:39:58 localhost.localdomain bash[10262]: I1112 23:39:58.631191   10262 proxier.go:427] Adding new service "default/kubernetes:https" at 10.123.240.1:443/TCP
    Nov 12 23:39:58 localhost.localdomain bash[10262]: I1112 23:39:58.632505   10262 proxier.go:647] Not syncing iptables until Services and Endpoints have been received from master
    Nov 12 23:39:58 localhost.localdomain bash[10262]: I1112 23:39:58.641024   10262 proxier.go:502] Setting endpoints for "default/kubernetes:https" to [10.64.33.81:6443]

    [tangfx@localhost ~]$ sudo journalctl --no-tail --no-pager --pager-end --lines=10 --unit=kube-proxy.service    

Load `POD image`

    [tangfx@localhost ~]$ curl http://192.168.1.100:48080
    <pre>
    <a href="centos/">centos/</a>
    <a href="gcr.io%252Fgoogle_containers/">gcr.io%2Fgoogle_containers/</a>
    </pre>

    [tangfx@localhost ~]$ curl http://192.168.1.100:48080/gcr.io%252Fgoogle_containers/
    <pre>
    <a href="gcr.io%252Fgoogle_containers%252Fkubernetes-dashboard-amd64%253Av1.0.1.tar">gcr.io%2Fgoogle_containers%2Fkubernetes-dashboard-amd64%3Av1.0.1.tar</a>
    <a href="gcr.io%252Fgoogle_containers%252Fkubernetes-dashboard-amd64%253Av1.1.1.tar">gcr.io%2Fgoogle_containers%2Fkubernetes-dashboard-amd64%3Av1.1.1.tar</a>
    <a href="gcr.io%252Fgoogle_containers%252Fkubernetes-dashboard-amd64%253Av1.4.0.tar">gcr.io%2Fgoogle_containers%2Fkubernetes-dashboard-amd64%3Av1.4.0.tar</a>
    <a href="gcr.io%252Fgoogle_containers%252Fkubernetes-dashboard-amd64%253Av1.4.2.tar">gcr.io%2Fgoogle_containers%2Fkubernetes-dashboard-amd64%3Av1.4.2.tar</a>
    <a href="gcr.io%252Fgoogle_containers%252Fpause%253A0.8.0.tar">gcr.io%2Fgoogle_containers%2Fpause%3A0.8.0.tar</a>
    <a href="gcr.io%252Fgoogle_containers%252Fpause%253A2.0.tar">gcr.io%2Fgoogle_containers%2Fpause%3A2.0.tar</a>
    <a href="gcr.io%252Fgoogle_containers%252Fpause-amd64%253A3.0.tar">gcr.io%2Fgoogle_containers%2Fpause-amd64%3A3.0.tar</a>
    </pre>

    [tangfx@localhost ~]$ curl http://192.168.1.100:48080/gcr.io%252Fgoogle_containers/gcr.io%252Fgoogle_containers%252Fpause-amd64%253A3.0.tar -O
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100  745k  100  745k    0     0  34716      0  0:00:21  0:00:21 --:--:-- 54639

    [tangfx@localhost ~]$ docker load -i gcr.io%252Fgoogle_containers%252Fpause-amd64%253A3.0.tar



Validation

    [tangfx@localhost ~]$ KUBECONFIG=.pki/kubernetes/kubeconfig kubectl get nodes
    NAME          STATUS    AGE
    10.64.33.81   Ready     20h
    10.64.33.90   Ready     12h