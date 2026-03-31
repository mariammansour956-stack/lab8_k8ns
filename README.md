# Kubernetes Assignment 8 – Node Scheduling, Affinity, Taints, QoS, DaemonSet & Static Pods

This repository contains all tasks for **Kubernetes Assignment 7** executed on **Minikube**. The tasks cover:

1. Pod scheduling using `nodeName`
2. NodeSelector (label-based scheduling)
3. Taints & Tolerations
4. Node Affinity (required vs preferred)
5. QoS Classes (Guaranteed, Burstable, BestEffort)
6. DaemonSet (one Pod per node)
7. Static Pod creation via manifest directory

---

## 🔹 Task 1: Schedule Pod using `nodeName`

**Pod:** `static-pinned`  
**Image:** `nginx:latest`

### Commands:

```bash
kubectl get nodes
kubectl apply -f task1_nodeName.yaml
kubectl get pod static-pinned -o wide
echo "--- Task 1: nodeName Pod ---" > k8s_proof.txt
kubectl get pod static-pinned -o wide >> k8s_proof.txt
🔹 Task 2: NodeSelector

Pod: selector-demo
Label: env=lab
Image: nginx:latest

kubectl label node minikube env=lab
kubectl apply -f task2_nodeselector.yaml
kubectl label node minikube env-
kubectl apply -f task2_pending.yaml
echo -e "\n--- Task 2: NodeSelector & Pending Status ---" >> k8s_proof.txt
kubectl get pod selector-demo -o wide >> k8s_proof.txt
kubectl get pod pending-selector >> k8s_proof.txt 2>&1
🔹 Task 3: Taints & Tolerations

Pods: no-tol, with-tol, evict-me

kubectl taint nodes minikube test=true:NoSchedule
kubectl run no-tol --image=nginx
kubectl apply -f task3_toleration.yaml
kubectl taint nodes minikube test=true:NoSchedule-
kubectl run evict-me --image=nginx
kubectl taint nodes minikube evict=now:NoExecute
kubectl get pods -w
kubectl taint nodes minikube evict=now:NoExecute-
echo -e "\n--- Task 3: NoExecute Eviction Proof ---" >> k8s_proof.txt
kubectl get pods | grep evict-me >> k8s_proof.txt
🔹 Task 4: Node Affinity

Pod: affinity-demo

Required: disktype=ssd
Preferred: zone=us-east
kubectl label node minikube disktype=ssd
kubectl apply -f task4_required_affinity.yaml
kubectl describe pod affinity-demo | grep -A15 Affinity
kubectl label node minikube disktype-
kubectl apply -f task4_preferred_affinity.yaml
🔹 Task 5: QoS Classes

Pods: guaranteed-pod, burstable-pod, besteffort-pod

kubectl apply -f task5_guaranteed.yaml
kubectl apply -f task5_burstable.yaml
kubectl apply -f task5_besteffort.yaml

kubectl describe pod guaranteed-pod | grep QoS
kubectl describe pod burstable-pod | grep QoS
kubectl describe pod besteffort-pod | grep QoS

echo -e "\n--- Task 5: QoS Classes Verification ---" >> k8s_proof.txt
kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.qosClass}{"\n"}{end}' >> k8s_proof.txt
🔹 Task 6: DaemonSet

DaemonSet: node-monitor
Image: nginx

kubectl apply -f task6_daemonset.yaml
kubectl get ds
kubectl get pods -o wide | grep node-monitor
kubectl get ds -n kube-system
🔹 Task 7: Static Pod

Pod: static-nginx
Manifest Path: /etc/kubernetes/manifests/

minikube ssh
sudo tee /etc/kubernetes/manifests/static-nginx.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: static-nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
EOF
exit

kubectl get pods
kubectl delete pod static-nginx-minikube
# Pod will come back automatically
minikube ssh
sudo rm /etc/kubernetes/manifests/static-nginx.yaml
exit

echo -e "\n--- Task 7: Static Pod Proof ---" >> k8s_proof.txt
kubectl get pods -o wide | grep "-minikube" >> k8s_proof.txt
✅ Notes
All YAML files are included in the repo for each task.
Proof commands save verification output in k8s_proof.txt.
Tasks were tested on Minikube Kubernetes cluster.
Concepts covered: nodeName, NodeSelector, Taints & Tolerations, Node Affinity, QoS Classes, DaemonSet, Static Pod.
