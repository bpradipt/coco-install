## Apply the configmaps
```
kubectl apply -f aws-podvm-image-cm.yaml
kubectl apply -f azure-podvm-image-cm.yaml
```

## Create podvm image
```
kubectl apply -f osc-podvm-create-job.yaml
```
On successful image creation, the image details will be updated in the `podvm-images`
configmap under `openshift-sandboxed-containers-operator` namespace.
The `podvm-images` configmap uses the cloud provider name (eg, aws, azure) to store the 
list of the images created.

The first entry in the list is the latest one.

For example, the following shows a sample o/p on Azure (few entries removed for brevity)

```
oc get cm -n openshift-sandboxed-containers-operator podvm-images -o yaml 

apiVersion: v1
data:
  azure: '/subscriptions/aaaaaaaa/resourceGroups/aro-lelfqxrs/providers/Microsoft.Compute/galleries/PodVMGallery/images/podvm-image/versions/0.0.2024020727
    /subscriptions/aaaaaaaa/resourceGroups/aro-lelfqxrs/providers/Microsoft.Compute/galleries/PodVMGallery/images/podvm-image/versions/0.0.2024020712 '
kind: ConfigMap
metadata:
  name: podvm-images
  namespace: openshift-sandboxed-containers-operator
```

On AWS setup
```
oc get cm -n openshift-sandboxed-containers-operator podvm-images -o yaml

apiVersion: v1
data:
  aws: ami-0e3b1983659f7c7dc ami-039b433de2bf8130d
kind: ConfigMap
metadata:
  name: podvm-images
  namespace: openshift-sandboxed-containers-operator
```

## Delete podvm image

Update the IMAGE_ID for Azure or AMI_ID for AWS that you want to delete and then run the following command

```
kubectl delete -f osc-podvm-delete-job.yaml
```

You can get the IMAGE_ID or AMI_ID from the `podvm-images` configmap.
For example, the following command will retrieve the latest azure image from the configmap

```
kubectl get cm -n openshift-sandboxed-containers-operator podvm-images -o jsonpath='{.data.azure}' | awk '{print $1}'
```

## PodVM image generation configuration

The configuration used for the podvm image generation is available in the following configmaps:

Azure: azure-podvm-image-cm
AWS: aws-podvm-image-cm
