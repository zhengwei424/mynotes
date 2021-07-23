## Core Concepts
Create namespace called practice. All following commands will be run into this namespace if not specified.

---
Create two pods with nginx image named nginx1 and nginx2 into your namespace. All of them should have the label app=v1.

---
Change pod nginx2 label to app=v2.

---
Get only pods with label app=v2 from all namespaces.

---
Remove the nginx pods to clean your namespace.

---
Create a messaging pod using redis:alpine image with label set to tier=msg. Check pod's labels.

---
Create a service called messaging-service to expose the messaging application within the cluster on port 6379 and describe it.

---
Create a busybox-echo pod that echoes 'hello world' and exits. After that check the logs.

---
Create an nginx-test pod and set an env value as var1=val1. Check the env value existence within the pod.

---
## Deployments
Create a deployment named hr-app using the image nginx:1.18 with 2 replicas.

---
Scale hr-app deployment to 3 replicas.

---
Update the hr-app image to nginx:1.19.

---
Check the rollout history of hr-app and confirm that the replicas are OK.

---
Undo the latest rollout and verify that new pods have the old image (nginx:1.18)

---
Do an update of the deployment with a wrong image nginx:1.91 and check the status.

---
Return the deployment to working state and verify the image is nginx:1.19.

---
## Scheduling
Shedule a nginx pod on specific node using NodeName.

---
Schedule a nginx pod based on node label using nodeSelector.

---
Taint a node with key=spray, value=mortein and effect=NoSchedule. Check that new pods are not scheduled on it.

---
Create another pod called nginx-toleration with nginx image, which tolerates the above taint.

---
Create a DaemonSet using image fluentd-elasticsearch:1.20.

---
Add label color=blue to one node and create nginx deployment called blue with 5 replicas and node Affinity rule to place the pods onto the labeled node.

---
## Configurations
Create a configmap named my-config with values key1=val1 and key2=val2. Check it's values.

---
Create a configMap called opt with value key5=val5. Create a new nginx-opt pod that loads the value from key key5 in an env variable called OPTIONS.

---
Create a configmap anotherone with values var6=val6 and var7=val7. Load this configmap as an env variables into a nginx-sec pod.

---
Create a configMap cmvolume with values var8=val8 and var9=val9. Load this as a volume inside an nginx-cm pod on path /etc/spartaa. Create the pod and 'ls' into the /etc/spartaa directory.

---
Create an nginx-requests pod with requests cpu=100m, memory=256Mi and limits cpu=200m, memory=512Mi.

---
Create a secret called mysecret with values password=mypass and check its yaml.

---
Create an nginx pod that mounts the secret mysecret in a volume on path /etc/foo.

---
## Observability
Get the list of nodes in JSON format and store it in a file.

---
Get CPU/memory utilization for nodes.

---
Create an nginx pod with a liveness probe that just runs the command ls. Check probe status.

---
Create an nginx pod (that includes port 80) with an HTTP readinessProbe on path '/' on port 80.

---
Use JSON PATH query to retrieve the osImages of all the nodes.

---
## Storage
Create a PersistentVolume of 1Gi, called 'myvolume-practice'. Make it have accessMode of 'ReadWriteOnce' and 'ReadWriteMany', storageClassName 'normal', mounted on hostPath '/etc/foo'. List all PersistentVolume

---
Create a PersistentVolumeClaim called 'mypvc-practice' requesting 400Mi with accessMode of 'ReadWriteOnce' and storageClassName of normal. Check the status of the PersistenVolume.

---
Create a busybox pod with command 'sleep 3600'. Mount the PersistentVolumeClaim mypvc-practice to '/etc/foo'. Connect to the 'busybox' pod, and copy the '/etc/passwd' file to '/etc/foo/passwd'.

---
Create a second pod which is identical with the one you just created (use different name). Connect to it and verify that '/etc/foo' contains the 'passwd' file. Delete the pods.

---
## Security
Create busybox-user pod that runs sleep for 1 hour and has user ID set to 101. Check the UID from within the container.

---
Create the YAML for an nginx pod that has capabilities "NET_ADMIN" and "SYS_TIME".

---
Create a new service account with the name pvviewer-practice. Grant this Service account access to list all PersistentVolumes in the cluster by creating an appropriate cluster role called pvviewer-role-practice and ClusterRoleBinding called pvviewer-role-binding-practice

---
## Networking
Create a pod with image nginx called nginx-1 and expose its port 80.

---
Get service's ClusterIP, create a temp busybox-1 pod and 'hit' that IP with wget.

---
Convert the ClusterIP to NodePort for the same service and find the NodePort. Hit the service(create temp busybox pod) using Node's IP and Port.

---
Create an nginx-last deployment of 2 replicas, expose it via a ClusterIP service on port 80. Create a NetworkPolicy so that only pods with labels 'access: granted' can access the deployment.

---
## Challenging
Create an nginx pod called nginx-resolver using image nginx, expose it internally with a service called nginx-resolver-service. Test that you are able to look up the service and pod names from within the cluster. Use the image: busybox:1.28 for dns lookup.

---
List the InternalIP of all nodes of the cluster.

---
Taint one worker node to be Unschedulable. Once done, create a pod called dev-redis with image redis:alpine to ensure workloads are not scheduled to the tainted node. Finally, create a new pod called prod-redis with image redis:alpine with toleration to be scheduled on the tainted node.

---
Create a Pod called redis-storage with image redis:alpine with a Volume of type emptyDir that lasts for the life of the Pod. Use volumeMount with mountPath = /data/redis.

---
Create a new deployment called nginx-deploy, with image nginx:1.16 and 1 replica. Record the version. Next upgrade the deployment to version 1.17 using rolling update. Make sure that the version upgrade is recorded in the resource annotation.
