HOWTO deploy ceph-nano on OpenShift 4.x

Original instructions how to run ceph-nano on Kubernetes can be found from the project website. https://github.com/ceph/cn

It's good to be aware cn kube generates a broken yaml and it will take while to fix everything (indendations, doubles and so on) -> check kube-cn-orig.yaml


oc login ...
oc new-project <myproject>
oc create sa anyuid

oc adm policy add-scc-to-user -n <myproject> -z anyuid anyuid

Retrieve storageClass and edit the kube-cn.yaml manifest 
kubectl get storageclass

oc create -f openshift-manifets/kube-cn.yaml

Replace the Host field in kube-cn.yaml manifest into the RGW_NAME env. variable and Route 
--RGW_NAME - value must match with route host name

oc apply -f openshift-manifets/kube-cn.yaml

Test :
1 - install https://s3tools.org/s3cmd (Mac user : brew install s3cmd)
2 - s3cmd --configure

New settings:
  Access Key: XXXX
  Secret Key: XXXX
  Default Region: US
  S3 Endpoint: ceph-nano-ceph-nano.apps.ocp4.ouachani.org
  DNS-style bucket+hostname:port template for accessing a bucket: ceph-nano-ceph-nano.apps.ocp4.ouachani.org
  Encryption password: ceph
  Path to GPG program: None
  Use HTTPS protocol: True
  HTTP Proxy server name: 
  HTTP Proxy server port: 0


3) Test it (example using s3cmd)
s3cmd mb s3://foobucket
date > test.txt
s3cmd put ./test.txt s3://foobucket
s3cmd ls s3://foobucket
s3cmd get s3://foobucket/test.txt froms3.txt


Difference between the original manifest provided by Ceph-nano :

1 - Indentation are incorrect
2 - Don't use default container image - Use (image ceph/nano is not stable and generate errors)
    registry.access.redhat.com/rhceph/rhceph-3-rhel7

3 - Route and env RGW_name to external access



