#  Up a Kubernetes Cluster on AWS in 5 Minutes

[Kubernetes](https://kubernetes.io/) is like magic. It is a system for working with containerized applications: deployment, scaling, management, service discovery, magic. Think Docker at scale with little hassle. Despite the power of Kubernetes though, I find the [official guide](https://github.com/kubernetes/kops/blob/master/docs/aws.md) for setting up Kubernetes on AWS a bit overwhelming, so I wrote a simpler version to get started.

As a side note, AWS introduced a new serviced called A [**mazon Elastic Container Service for Kubernetes**](https://aws.amazon.com/eks/) – EKS for short. But it’s still in Preview mode.

Before we begin, here’s a YouTube video demonstrating how to set up a Kubernetes Cluster on AWS following the instructions below:

# Prerequisites

Before setting up the Kubernetes cluster, you’ll need an [AWS account](https://aws.amazon.com/account/) and an installation of the [AWS Command Line Interface](https://aws.amazon.com/cli/).

Make sure to configure the AWS CLI to use your access key ID and secret access key:

```
aws configure
AWS Access Key ID [None]: your aws access key
AWS Secret Access Key [None]: your aws secret key
Default region name [None]: us-east-1
Default output format [None]:

```

Installing kops + kubectl
Now, to get started, let’s install two Kubernetes CLI utilities:

1. [Kubernetes Operations, kops](https://github.com/kubernetes/kops)
2. [Kubernetes command-line tool, kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)

On Mac OS X, we’ll use [brew](https://brew.sh/) to install. If you’re on Linux, see the official [Kops](https://github.com/kubernetes/kops#installing) installation guide.


# Setting Up the Kubernetes Cluster

Easy enough. Now, let’s set up the Kubernetes cluster.

The first thing we need to do is create an S3 bucket for kops to use to store the state of the Kubernetes cluster and its configuration. We’ll use the bucket name colors-kops-state-store.

```
$ aws s3api create-bucket --bucket colors-kops-state-store --region us-east-2 --create-bucket-configuration LocationConstraint=us-east-2

```

After creating the colors-kops-state-store bucket, let’s enable versioning to revert or recover a previous state store.

```
$ aws s3api put-bucket-versioning --bucket colors-kops-state-store  --versioning-configuration Status=Enabled

```
Before creating the cluster, let’s set two environment variables: KOPS_CLUSTER_NAME and **KOPS_STATE_STORE. **For safe keeping you should add the following to your **~/.bash_profile ** or ~/.bashrc configs (or whatever the equivalent is if you don’t use bash).


export KOPS_CLUSTER_NAME=colors.k8s.local
export KOPS_STATE_STORE=s3://colors-kops-state-store


kops export kubecfg --state s3://colors-kops-state-store --name=colors.k8s.local

You don’t HAVE TO set the environment variables, but they are useful and referenced by **kops** commands. For example, see ```kops create cluster --help```. If the the Kubernetes cluster name ends with k8s.local, Kubernetes will create a gossip-based cluster.

Now, to generate the cluster configuration:

```
$ kops create cluster --node-count=1 --node-size=t2.medium --zones=us-east-2b 
```

Note: this line doesn’t launch the AWS EC2 instances. It simply creates the configuration and writes to the s3://colors-kops-state-store bucket we created above. In our example, we’re creating 2 t2.medium EC2 work nodes in addition to a c4.large master instance (default).

```
$ kops edit cluster
```

Alternatively, you can name the cluster by appending --name to the command:

```
$ kops create cluster --node-count=1 --node-size=t2.medium --zones=us-east-1a --name chubby-bunnies
```

Now that we’ve generated a cluster configuration, we can edit its description before launching the instances. The config is loaded from s3://colors-kops-state-store. You can change the editor used to edit the config by setting $EDITOR or $KUBE_EDITOR. For instance, in my ~/.bashrc, I have export KUBE_EDITOR=emacs.


Time to build the cluster. This takes a few minutes to boot the EC2 instances and download the Kubernetes components.

kops update cluster --name ${KOPS_CLUSTER_NAME} --yes
After waiting a bit, let’s validate the cluster to ensure the master + 2 nodes have launched.

$ kops validate cluster
Validating cluster colors.k8s.local

INSTANCE GROUPS

NAME			ROLE	MACHINETYPE	MIN	MAX	SUBNETS

master-us-east-1a	Master	c4.large	1	1	us-east-1a
nodes			Node	t2.medium	2	2	us-east-1a


NODE STATUS
NAME				ROLE	READY
ip-172-20-34-111.ec2.internal	node	True
ip-172-20-40-24.ec2.internal	master	True
ip-172-20-62-139.ec2.internal	node	True
Note: If you ignore the message Cluster is starting. It should be ready in a few minutes. and validate too early, you’ll get an error. Wait a little longer for the nodes to launch, and the validate step will return without error.

$ kops validate cluster
Validating cluster colors.k8s.local

unexpected error during validation: error listing nodes: Get https://api-colors-k8s-local-71cb48-202595039.us-east-1.elb.amazonaws.com/api/v1/nodes: EOF
Finally, you can see your Kubernetes nodes with kubectl:

$ kubectl get nodes
NAME                            STATUS    ROLES     AGE       VERSION
ip-172-20-34-111.ec2.internal   Ready     node      2h        v1.9.3
ip-172-20-40-24.ec2.internal    Ready     master    2h        v1.9.3
ip-172-20-62-139.ec2.internal   Ready     node      2h        v1.9.3



# Kubernetes Dashboard

Excellent. We have a working Kubernetes cluster deployed on AWS. At this point, we can deploy lots of things, such as Dask and Jupyter. For demonstration, we’ll launch the Kubernetes Dashboard. Think UI instead of command line for managing Kubernetes clusters and applications.

Here’s a YouTube video illustrating how to install the Kubernetes Dashboard:

```

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml

secret "kubernetes-dashboard-certs" created
serviceaccount "kubernetes-dashboard" created
role.rbac.authorization.k8s.io "kubernetes-dashboard-minimal" created
rolebinding.rbac.authorization.k8s.io "kubernetes-dashboard-minimal" created
deployment.apps "kubernetes-dashboard" created
service "kubernetes-dashboard" created

```



You can see that various items were created, including the kubernetes-dashboard service and app.


If the dashboard was created, how do we view it? Easy. Let’s get the AWS hostname:

```
$ kubectl cluster-info

Kubernetes master is running at https://api-ramhiser-k8s-local-71cb48-202595039.us-east-1.elb.amazonaws.com
KubeDNS is running at https://api-ramhiser-k8s-local-71cb48-202595039.us-east-1.elb.amazonaws.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

```


With this hostname, open your browser to https://api-ramhiser-k8s-local-71cb48-202595039.us-east-1.elb.amazonaws.com/ui. (You’ll need to replace the hostname with yours).


Alternatively, you can access the Dashboard UI via a proxy:


```
 kubectl proxy

Starting to serve on 127.0.0.1:8001
```
https://api-colors-k8s-local-inoltk-1851277883.us-east-2.elb.amazonaws.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login

login password nyY0Wm0ocm63cea1TnkA9G0cZkD3vEkd
admin token  XPjiuCWjRGM7ScVbmIoWtUU3ljsHnt04


# Adding team member is kubernetes project

```
https://itnext.io/let-you-team-members-use-kubernetes-bf2ebd0be717
```




helmins() {
 kubectl -n kube-system create serviceaccount tiller
 kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
 helm init --service-account=tiller
}
helmdel() {
 kubectl -n kube-system delete deployment tiller-deploy
 kubectl delete clusterrolebinding tiller
 kubectl -n kube-system delete serviceaccount tiller
 
}


kubectl port-forward nonexistent-labradoodle-colors-helm-7cf6d7f88d-66dh5 8080:80


to get passs

kops get secrets kube --type secret -oplaintext


to get token 
kops get secrets admin --type secret -oplaintext


## To use kubectl in insecure mod

```
kubectl --insecure-skip-tls-verify --context=colors.k8s.local get nodes

```