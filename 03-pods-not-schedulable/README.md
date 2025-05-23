In Kubernetes, the scheduler is responsible for assigning pods to nodes in the cluster based on various criteria. Sometimes, you might encounter situations where pods are not being scheduled as expected. This can happen due to factors such as node constraints, pod requirements, or cluster configurations.

1. Node Selector

Node Selector is a simple way to constrain pods to nodes with specific labels. It allows you to specify a set of key-value pairs that must match the node's labels for a pod to be scheduled on that node.
Usage: Include a nodeSelector field in the pod's YAML definition to specify the required labels.

```
spec:
    containers:
    - name: my-app
    image: my-image
    nodeSelector:
    disktype: ssd
```

2. Node Affinity

Node Affinity is a more expressive way to specify rules about the placement of pods relative to nodes' labels. It allows you to specify rules that apply only if certain conditions are met.
Usage: Define nodeAffinity rules in the pod's YAML definition, specifying required and preferred node selectors.

```
spec:
    containers:
    - name: my-app
    image: my-image
    affinity:
    nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
            - key: disktype
            operator: In
            values:
            - ssd
```

INTERVIEW QUESTION
```
Difference between Node Selector and Node affinity?
A. Although the feature are little similar, node affinity grants more granular level of configuration OR provides more flexibility for the Devops engineer which says kube scheduler that you can go with preferred configuration and if you don't find the preferred configuration, you can schedule it anywhere 
```

K8 doc for taints and tolerations:
```
https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
```

3. Taints

Taints are applied to nodes to repel certain pods. They allow nodes to refuse pods unless the pods have a matching toleration.  ||  
Using taint you can decide what's the behaviour of the node during the upgrades. You can taint(unschedule) a particular node as unscheduled so that your node can go to the unscheduled status, u can take that node and perform upgrades_ Hence, that's why master nodes BY DEFAULT ARE TAINTED as no pods can be scheduled on master nodes.
```
Three types of taints that are most popular : 
Noschedule - Where ur node will go to complete non-schedulable status. 
Noexecute - It's little bit dangerous, becoz when u do "Noexecute", all the pods that are on particular node will immediately stop working. 
Preferred Noschedule- If u any performance issue with a node, you can take "Preferred noschedule" taint on that particular node where it will be scheduled but in a worst case, if u taint a particular node as "Prefer Noschedule", u might still see some pods running on that particluar node but that is bacuse all ur other nodes are busy and there is no otpion for ur schedular

```
Usage: Use kubectl taint command to apply taints to nodes. Include tolerations field in the pod's YAML definition to tolerate specific taints.

```
kubectl taint nodes node1 disktype=ssd:NoSchedule
```

```
spec:
    containers:
    - name: my-app
    image: my-image
    tolerations:
    - key: disktype
      operator: Equal
      value: ssd
      effect: NoSchedule
```

4. Tolerations

Tolerations is basically an exception that is given to certain pods. 
Tolerations are applied to pods and allow them to schedule onto nodes with matching taints. They override the effect of taints.

Usage: Include tolerations field in the pod's YAML definition to specify which taints the pod tolerates.

```
spec:
  containers:
  - name: my-app
    image: my-image
  tolerations:
  - key: disktype
    operator: Equal
    value: ssd
    effect: NoSchedule
```
