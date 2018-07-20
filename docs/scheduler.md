---
id: scheduler
title: Scheduling OpenEBS control plane and data plane pods
sidebar_label: Scheduler
---

------

OpenEBS **DOES NOT** have a separate scheduler of it's own to manage the scheduling of it's pods. It uses Kubernetes scheduler for managing the scheduling needs of administrator. 

OpenEBS deals with many types of Kubernetes pods in it's life cycle. These can be broadly categorized into two types.

- **Control plane pods**
  - OpenEBS API Server
  - OpenEBS Provisioner
  - OpenEBS Snapshot controller
- **Data plane pods**
  - Jiva data plane 
    - Jiva storage target pod
    - Jiva storage replica pod(s)
  - cStor data plane `(Initial availability from 0.7 release)`
    - cStor storage target pod
    - cStor storage replica pod(s)

Control plane pods are scheduled during the installation of OpenEBS. Data plane pods are scheduled during the provisioning of volumes. 

## Scheduling control plane pods

Administrator can choose to provide the scheduling configuration for control plane pods during the time of installation or post that. 

### Before OpenEBS installation

**Step1:** Download openebs-operator file.

`wget  https://raw.githubusercontent.com/openebs/openebs/master/k8s/openebs-operator.yaml `

**Step2:** Modify the configuration for scheduling in the file openebs-operator.yaml  either using NODE_SELECTOR  method or using TAINTS method as shown below.

#### NODE_SELECTOR method (preferred method): ([Why?](/docs/next/scheduler.html#selecting-between-node-selector-method-taint-methods))

**Update the NODE_SELECTOR for openebs-provisioner**

`kubectl label nodes <node-name> openebs=controlnode`

```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: openebs-provisioner
  namespace: default
spec:
  replicas: 1
  nodeSelector:
    openebs: controlnode
  template:
    metadata:
      labels:
        name: openebs-provisioner
        
```

**Update the NODE_SELECTOR for maya-apiserver**

`kubectl label nodes <node-name> openebs=controlnode

```
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: maya-apiserver
  namespace: default
spec:
  replicas: 1
  nodeSelector:
    openebs: controlnode
  template:
    metadata:
      labels:
        name: maya-apiserver
```

**Update the NODE_SELECTOR for openebs-snapshot-controller-apiserver**

`kubectl label nodes <node-name> openebs=controlnode

```
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: openebs-snapshot-controller
  namespace: openebs
spec:
  replicas: 1
  nodeSelector:
    openebs: controlnode
  strategy:
    type: Recreate
```





#### TAINTS method:



**Step 3:** Run the operator file to install OpenEBS and schedule the OpenEBS control planes pods on the required node(s) 

`kubectl apply -f ./openebs-operator.yaml `



### When OpenEBS is already installed

#### NODE-SELECTOR method ([Why?](/docs/next/scheduler.html#selecting-between-node-selector-method-taint-methods))

#### TAINTS method

## Scheduling data plane pods

### Before OpenEBS installation

#### NODE-SELECTOR method ([Why?](/docs/next/scheduler.html#selecting-between-node-selector-method-taint-methods))

#### TAINTS method

### When OpenEBS is already installed

#### NODE-SELECTOR method ([Why?](/docs/next/scheduler.html#selecting-between-node-selector-method-taint-methods))

#### TAINTS method



## Selecting between NODE-SELECTOR method TAINT method 

Kubernetes provides these two methods to control scheduling of pods on cluster nodes. For more details on these features you can refer to the Kubernetes documentation on [NODE-SELECTORS](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) and [Taints&Tolerations](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/)

In general NODE-SELECTOR method is suitable for assigning OpenEBS control plane pods to a set of given nodes as there is a direct representation of where the pods have to go. For example, administrator can choose to configure the control pods on (N1,N2) or (N3,N4,N5).

Taints and tolerations method is recommended when the administrator wants to dedicate only certain nodes for OpenEBS Jiva pods (target or replica). For example, out of 20 nodes in a given Kubernetes cluster, if Jiva pods have to be dedicated for 3 nodes (N8,N9,N10) then taints are setup on N8,N9 and N10 and tolerations are provided to Maya-Apiserver. 

 

<!-- Hotjar Tracking Code for https://docs.openebs.io -->
<script>
   (function(h,o,t,j,a,r){
       h.hj=h.hj||function(){(h.hj.q=h.hj.q||[]).push(arguments)};
       h._hjSettings={hjid:785693,hjsv:6};
       a=o.getElementsByTagName('head')[0];
       r=o.createElement('script');r.async=1;
       r.src=t+h._hjSettings.hjid+j+h._hjSettings.hjsv;
       a.appendChild(r);
   })(window,document,'https://static.hotjar.com/c/hotjar-','.js?sv=');
</script>