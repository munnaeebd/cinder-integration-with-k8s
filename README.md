# cinder-integration-with-k8s

## On Master node:
~~~
 vi /etc/kubernetes/manifests/kube-controller-manager.yaml
 
 - --cloud-provider=external
 ~~~
 
 ## On all the node: 
 ~~~
  vi /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
[Service]
Environment="KUBELET_KUBECONFIG_ARGS=--cloud-provider=external --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"

systemctl daemon-reload
systemctl restart kubelet
~~~
~~~
root@mn-master-node:~# cat /etc/kubernetes/cloud.conf

[Global]
auth-url=http://cloud.brilliant.com.bd:5000/v3
#Tip: You can also use Application Credential ID and Secret in place of username, password, tenant-id, and domain-id.
#application-credential-id=
#application-credential-secret=
username=
# user-id=
password=
region=RegionOne
tenant-id=
domain-id=default

[BlockStorage]
bs-version=v2
~~~
~~~
kubectl create secret generic -n kube-system cloud-config --from-file=/etc/kubernetes/cloud.conf

kubectl apply -f https://raw.githubusercontent.com/kubernetes/cloud-provider-openstack/master/manifests/controller-manager/cloud-controller-manager-roles.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/cloud-provider-openstack/master/manifests/controller-manager/cloud-controller-manager-role-bindings.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/cloud-provider-openstack/master/manifests/controller-manager/openstack-cloud-controller-manager-ds.yaml
~~~
~~~
git clone https://github.com/kubernetes/cloud-provider-openstack.git

 cd cloud-provider-openstack/
 rm -f manifests/cinder-csi-plugin/csi-secret-cinderplugin.yaml
 kubectl -f manifests/cinder-csi-plugin/ apply
~~~
~~~
vi csi-sc-cinder.yaml

apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: cinder-sc
provisioner: cinder.csi.openstack.org
~~~

## For Specific Type Volume like SSD

~~~
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: cinder-sc
provisioner: cinder.csi.openstack.org
parameters:
  type: SF-SSD
~~~

