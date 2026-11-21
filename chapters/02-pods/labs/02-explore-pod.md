# Lab 02: Explore Pod behavior

এই lab-এ standalone Pod তৈরি করে object, container state, shared network namespace, restart এবং disposable identity পর্যবেক্ষণ করব।

> **Safety:** নিজের local Kind cluster ব্যবহার করো। Active context নিশ্চিত করো। Cleanup শুধু এই lab-এর Pod মুছবে।

[← Chapter 02: Pods](../README.md)

## 1. Context যাচাই

```bash
kubectl config current-context
kubectl get nodes
```

নিজের local lab context select করো।

## 2. Pod manifest

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-explorer
  labels:
    lab: pod-explorer
spec:
  restartPolicy: Always
  containers:
    - name: web
      image: nginx:1.27
      ports:
        - containerPort: 80
      resources:
        requests:
          cpu: 100m
          memory: 64Mi
        limits:
          cpu: 250m
          memory: 128Mi
    - name: helper
      image: curlimages/curl:8.10.1
      command: ["sh", "-c", "sleep 3600"]
      resources:
        requests:
          cpu: 50m
          memory: 32Mi
        limits:
          cpu: 100m
          memory: 64Mi
```

ফাইলটি `pod-explorer.yaml` নামে save করে:

```bash
kubectl apply -f pod-explorer.yaml
kubectl get pod pod-explorer -o wide
kubectl describe pod pod-explorer
```

Pod status, IP, Node, image, resource ও events পর্যবেক্ষণ করো।

## 3. Metadata, spec, status

```bash
kubectl get pod pod-explorer -o yaml
kubectl get pod pod-explorer -o jsonpath='{.metadata.labels}{"\n"}'
kubectl get pod pod-explorer -o jsonpath='{.spec.containers[*].name}{"\n"}'
kubectl get pod pod-explorer -o jsonpath='{.status.phase}{"\n"}'
kubectl get pod pod-explorer -o jsonpath='{.status.conditions[?(@.type=="Ready")].status}{"\n"}'
```

Output থেকে metadata, desired spec এবং observed status identify করো।

## 4. Shared localhost পরীক্ষা

```bash
kubectl exec pod-explorer -c helper -- curl -sS http://127.0.0.1/
```

Nginx welcome HTML পাওয়া উচিত। Helper-এর localhost app container-এর localhost, কারণ Pod container একই network namespace share করে।

## 5. Pod IP দেখো

```bash
kubectl get pod pod-explorer -o jsonpath='{.status.podIP}{"\n"}'
```

IP নোট করো। Pod recreate হলে IP বদলাতে পারে, তবে বদলানো নিশ্চিত নয়।

## 6. Container restart observe করো

```bash
kubectl exec pod-explorer -c helper -- sh -c 'exit 1'
kubectl get pod pod-explorer
kubectl get pod pod-explorer -o jsonpath='{.status.containerStatuses[*].name}{"\n"}{.status.containerStatuses[*].restartCount}{"\n"}'
kubectl describe pod pod-explorer
```

Helper restart হতে পারে। Restart timing/backoff-এর কারণে তাৎক্ষণিক output ভিন্ন হতে পারে। Restart count দেখো; Pod UID একই থাকার কথা।

## 7. Pod identity তুলনা

```bash
kubectl get pod pod-explorer -o jsonpath='{.metadata.uid}{"\n"}{.status.podIP}{"\n"}'
kubectl delete pod pod-explorer
kubectl apply -f pod-explorer.yaml
kubectl get pod pod-explorer -o wide
kubectl get pod pod-explorer -o jsonpath='{.metadata.uid}{"\n"}{.status.podIP}{"\n"}'
```

নতুন UID হবে। IP একই বা ভিন্ন হতে পারে; IP পরিবর্তন guaranteed নয়। UID পরিবর্তন নতুন Pod object হওয়ার evidence।

## 8. Cleanup

```bash
kubectl delete pod pod-explorer
```

## Checkpoint
- Pod-এর দুই container localhost share করে কেন?
- Container restart হলে UID কি বদলায়?
- Pod delete/recreate করলে UID কেন বদলায়?
- Running মানেই readiness নিশ্চিত কি?
- Production-এ standalone Pod-এর বদলে Deployment কেন ব্যবহার করা হয়?
