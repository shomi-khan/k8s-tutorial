# Lab 03: Deploy, scale, self-heal and rollout

এই lab-এ Deployment তৈরি করে Deployment → ReplicaSet → Pod relationship observe করব। তারপর Pod delete, scale, image update ও rollback করে controller behavior দেখব।

> **Safety:** নিজের local Kind cluster ব্যবহার করো। Active context নিশ্চিত করো। Cleanup শুধু এই lab-এর `workload-lab` Deployment delete করবে।

[← Chapter 03: Workloads](../README.md)

## 1. Context ও cluster যাচাই

```bash
kubectl config current-context
kubectl get nodes
```

Local Kind context নিশ্চিত করো।

## 2. Deployment manifest তৈরি


Lab-এর directory-তে একটি file তৈরি করো:


```bash
cat > workload-lab.yaml <<'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: workload-lab
  labels:
    app: workload-lab
spec:
  replicas: 3
  selector:
    matchLabels:
      app: workload-lab
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  template:
    metadata:
      labels:
        app: workload-lab
    spec:
      containers:
        - name: web
          image: nginx:1.27
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: 50m
              memory: 64Mi
            limits:
              cpu: 200m
              memory: 128Mi
EOF
```

Manifest review করে তারপর Apply এবং observe:

```bash
kubectl apply -f workload-lab.yaml
kubectl rollout status deployment/workload-lab
kubectl get deployment,rs,pods -l app=workload-lab -o wide
```

একটি Deployment, একটি ReplicaSet ও তিনটি Pod দেখতে পাওয়ার কথা। Resource ready হতে সময় লাগতে পারে।

## 3. Ownership ও template inspect করো

```bash
kubectl describe deployment workload-lab
kubectl get rs -l app=workload-lab
kubectl get pods -l app=workload-lab --show-labels
```

Pod owner reference এবং ReplicaSet-এর label/template-এর relationship inspect করতে:

```bash
kubectl get pods -l app=workload-lab -o jsonpath='{range .items[*]}{.metadata.name}{" owner="}{.metadata.ownerReferences[0].name}{" uid="}{.metadata.uid}{"\n"}{end}'
```

প্রতিটি Pod কোন ReplicaSet-এর owned, এবং Pod নামগুলো কেন একই prefix-এর, তা explain করার চেষ্টা করো।

## 4. Self-healing observe করো

একটি Pod নাম বের করো:

```bash
kubectl get pods -l app=workload-lab
```

একটি Pod delete করো (নিচের command-এ actual Pod name বসাও)। নিশ্চিত হও নামটি `workload-lab` label-এর Pod থেকেই নেওয়া:

```bash
kubectl delete pod <pod-name>
kubectl get pods -l app=workload-lab -w
```

Watch শেষ করতে Ctrl+C চাপো। নতুন Pod আসতে পারে, তার UID/name পুরোনো Pod-এর থেকে আলাদা হবে। Deployment/ReplicaSet desired count পূরণের চেষ্টা করছে।

## 5. Scale up ও down

```bash
kubectl scale deployment/workload-lab --replicas=5
kubectl get deployment,rs,pods -l app=workload-lab
kubectl scale deployment/workload-lab --replicas=2
kubectl get deployment,rs,pods -l app=workload-lab
```

প্রতিটি command-এর আগে অনুমান করো: কত Pod তৈরি/terminate হবে? Scale command desired state পরিবর্তন করে; controller-এর কাজ সম্পূর্ণ হতে সময় লাগে।

## 6. Rolling update

Image version পরিবর্তন করো:

```bash
kubectl set image deployment/workload-lab web=nginx:1.28
kubectl rollout status deployment/workload-lab
kubectl get deployment,rs,pods -l app=workload-lab
kubectl rollout history deployment/workload-lab
```

Observe করো:
- নতুন ReplicaSet তৈরি হয়েছে কি?
- পুরোনো ও নতুন ReplicaSet কিছু সময় একসঙ্গে ছিল কি?
- নতুন Pod ready হওয়া এবং পুরোনো Pod কমানোর ক্রম কী?
- Rollout শেষে কত replica আছে?

## 7. Rollback

```bash
kubectl rollout undo deployment/workload-lab
kubectl rollout status deployment/workload-lab
kubectl rollout history deployment/workload-lab
kubectl get deployment,rs,pods -l app=workload-lab
```

Rollback-এর পর Deployment template কোন image version-এ আছে তা inspect করো:

```bash
kubectl get deployment workload-lab -o jsonpath='{.spec.template.spec.containers[0].image}{"\n"}'
```

Rollback application data বা external side effect reverse করে না; এটি Deployment revision ফিরিয়ে দেয় এবং controller নতুন desired template reconcile করে।

## 8. Failure investigation (ঐচ্ছিক)

Deployment status এবং event observe করো:

```bash
kubectl describe deployment workload-lab
kubectl get events --sort-by=.metadata.creationTimestamp
```

কোন Pod Pending/NotReady হলে তার describe output-এ event খুঁজে দেখো। Cluster resource কম থাকলে experiment-টি skip করতে পারো। এই lab-এর resource request ছোট রাখা হয়েছে, কিন্তু cluster capacity environment অনুযায়ী আলাদা।

## 9. Cleanup

```bash
kubectl delete deployment workload-lab
```

Deployment delete হলে তার managed ReplicaSet ও Pods cascading deletion-এ delete হওয়ার কথা। Verify:

```bash
kubectl get deployment,rs,pods -l app=workload-lab
```

## Checkpoint

- Deployment, ReplicaSet ও Pod কীভাবে সম্পর্কিত?
- Pod delete করার পর replacement কে তৈরি করেছে?
- Scale command কোন state পরিবর্তন করে?
- Image update-এর পর নতুন ReplicaSet কেন তৈরি হয়?
- Rollback করার পর পুরোনো ReplicaSet কেন আবার দেখা যায়/scale হয়?
- `maxSurge: 1` এবং `maxUnavailable: 0` rollout-এ কী বোঝায়?
