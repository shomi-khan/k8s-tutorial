# Lab 01: Inspect a Kubernetes Cluster

এই lab-এর উদ্দেশ্য নতুন workload deploy করা নয়। উদ্দেশ্য হলো একটি Kubernetes Cluster-এর logical architecture-কে বাস্তব cluster-এর সঙ্গে মিলিয়ে দেখা।

আমরা Kind ব্যবহার করব। Kind local Docker container-কে Kubernetes Node হিসেবে ব্যবহার করে। ফলে নিজের machine-এ Control Plane ও Node-এর কিছু বাস্তব component inspect করা যায়।

> **সতর্কতা:** এই lab নিজের local environment-এ চালাও। এটি কোনো production cluster পরিবর্তন করার জন্য নয়।

[← Chapter 01: Kubernetes Architecture](../README.md)

## 1. Prerequisites যাচাই

এই lab-এর জন্য প্রয়োজন:

- Docker
- Kind
- kubectl

প্রথমে version যাচাই করো:

```bash
docker version
kind version
kubectl version --client
```

Docker daemon চালু আছে কি না নিশ্চিত হও। Docker command permission error দিলে আগে local Docker setup ঠিক করো।

## 2. Local cluster তৈরি

```bash
kind create cluster --name architecture-lab
```

Kind cluster তৈরি করে kubectl context configure করার চেষ্টা করবে।

Context যাচাই করো:

```bash
kubectl config current-context
kubectl cluster-info
```

Expected context হবে:

```text
kind-architecture-lab
```

যদি তোমার machine-এ অন্য context active হয়ে যায়, পরের command চালানোর আগে সঠিক context select করো:

```bash
kubectl config use-context kind-architecture-lab
```

## 3. Cluster ও Node inspect করো

```bash
kubectl get nodes -o wide
```

Kind-এর default configuration-এ সাধারণত একটি Node দেখতে পাবে। Node-এর নাম হবে `architecture-lab-control-plane`।

এখানে খেয়াল করো:

- Kubernetes-এর কাছে Node একটি API object।
- Node-এর status `Ready` কি না।
- Node-এর internal IP ও Kubernetes version কী।
- Node-এর role label কী।

আরও বিস্তারিত:

```bash
kubectl describe node architecture-lab-control-plane
```

Output-এর `Conditions`, `Capacity`, `Allocatable`, `Addresses` এবং `Non-terminated Pods` অংশগুলো দেখো।

**ভেবে দেখো:** Node object দেখা যাচ্ছে মানেই কি Node-এর সব application healthy? Node readiness আর কোনো নির্দিষ্ট application-এর readiness এক জিনিস নয়।

## 4. Control Plane component inspect করো

```bash
kubectl get pods -n kube-system -o wide
```

Kind-এর default cluster-এ সাধারণত নিচের মতো system Pod দেখতে পারো:

- `kube-apiserver-...`
- `etcd-...`
- `kube-controller-manager-...`
- `kube-scheduler-...`
- `kube-proxy-...`
- `coredns-...`
- Kind network plugin-এর Pod, যেমন `kindnet-...`

প্রথম চারটি Control Plane-এর গুরুত্বপূর্ণ component। CoreDNS ও network plugin cluster add-on; এগুলোকে Control Plane-এর core component ভেবে নিও না।

এবার শুধু Control Plane static Pod-গুলো filter করে দেখো:

```bash
kubectl get pods -n kube-system -o wide | grep -E 'kube-apiserver|etcd-|kube-controller-manager|kube-scheduler'
```

এই Pod-গুলো কোন Node-এ চলছে, সেটি `NODE` column-এ দেখো।

Kind-এর default single-node cluster-এ Control Plane component এবং Node-level agent একই Kind node container-এর ভিতরে থাকতে পারে। Logical role আলাদা হলেও physical machine/container আলাদা হওয়া বাধ্যতামূলক নয়।

## 5. Kind Node container-এর ভেতরে runtime inspect করো

Kind-এর Node-গুলো Docker container হিসেবে চলে। Docker থেকে সেগুলো দেখো:

```bash
docker ps --filter "name=architecture-lab"
```

এবার Kind control-plane container-এর ভিতরে চলা container runtime-এর container list দেখো:

```bash
docker exec architecture-lab-control-plane crictl ps
```

এখানে Kubernetes system Pod-এর container-ও দেখতে পারবে। `kubectl get pods` API-level object দেখায়; `crictl ps` node-এর container runtime-এ চলা container দেখায়।

এ দুটি output একই abstraction level-এর নয়।

> যদি `crictl` command পাওয়া না যায়, Kind node image বা runtime tooling-এর version ভিন্ন হতে পারে। সে ক্ষেত্রে আগের `kubectl` ও `docker ps` inspection দিয়েই lab-এর মূল লক্ষ্য পূরণ করা যাবে।

## 6. Node lease inspect করো

Kubernetes Node-এর heartbeat ও liveness সম্পর্কিত তথ্যের জন্য Lease object ব্যবহার করে।

```bash
kubectl get leases -n kube-node-lease
```

তোমার Kind cluster-এ Node-এর নামের সঙ্গে মিল থাকা Lease দেখতে পারো।

এখানে লক্ষ্য হলো Node object এবং Lease object আলাদা resource হিসেবে দেখা। Node heartbeat ও failure detection-এর internal behavior Chapter 10-তে বিস্তারিত দেখব।

## 7. API Server-এর health endpoint inspect করো

```bash
kubectl get --raw='/readyz?verbose'
```

API Server-এর readiness check-এর ফলাফল দেখবে। সব check সফল হলে সাধারণত শেষে `readyz check passed` দেখা যায়।

এটি API Server-এর health check; পুরো application বা সব Worker Node healthy, তার প্রমাণ নয়।

## 8. Architecture map তৈরি করো

এবার নিজের দেখা output ব্যবহার করে নিচের প্রশ্নগুলোর উত্তর লেখো:

| কী দেখেছ | কোন architecture concept-এর সঙ্গে সম্পর্কিত |
|---|---|
| `kubectl cluster-info` | API Server endpoint |
| `kubectl get nodes` | Cluster-এর Node object |
| `kube-apiserver` Pod | API entry point |
| `etcd` Pod | Persistent cluster state |
| `kube-scheduler` Pod | Pod placement decision |
| `kube-controller-manager` Pod | Built-in controller process |
| `crictl ps` | Node-এর container runtime |
| `kube-node-lease` Lease | Node heartbeat information |

নিজের ভাষায় একটি ছোট diagram আঁকো: kubectl থেকে API Server, API Server থেকে etcd, Controller/Scheduler, এবং Node-এর kubelet ও runtime পর্যন্ত।

## 9. Experiment ও observation

নিচের প্রতিটি প্রশ্নের উত্তর command output-এর evidence দিয়ে দাও:

1. তোমার Kind cluster-এ কয়টি Node আছে?
2. Control Plane-এর কোন component-গুলো `kube-system` namespace-এ দেখা যাচ্ছে?
3. System Pod-গুলো কোন Node-এ চলছে?
4. Docker-এর কাছে Kind Node কী হিসেবে দেখা যাচ্ছে?
5. `kubectl get pods` ও `crictl ps` কী আলাদা তথ্য দেখাচ্ছে?
6. Node-এর Lease object-এর নাম কী?
7. API Server readiness endpoint কী ফল দিয়েছে?

একবারে একটি command চালাও এবং output পর্যবেক্ষণ করো। শুধু expected output copy করাই lab-এর উদ্দেশ্য নয়।

## 10. Cleanup

Lab শেষ হলে local cluster মুছে ফেলো:

```bash
kind delete cluster --name architecture-lab
```

এটি শুধু এই lab-এর Kind cluster মুছে দেবে। অন্য Kind cluster বা Docker container-এ হাত দেবে না।

## Lab checkpoint

নিজের ভাষায় ব্যাখ্যা করতে পারলে lab সফল:

- Kind-এর Docker container কীভাবে Kubernetes Node হিসেবে কাজ করে?
- Control Plane-এর Pod এবং Node-level runtime container-এর মধ্যে সম্পর্ক কী?
- API Server, etcd, Scheduler ও kubelet-এর দায়িত্ব কীভাবে আলাদা?
- `kubectl` এবং `crictl` কেন ভিন্ন ধরনের তথ্য দেখায়?
- Control Plane component-এর নাম `kube-system` namespace-এ দেখা গেলেই কি সব cluster-এ একই deployment topology থাকবে?

পরের chapter-এ আমরা Pod-এর ভেতরের structure, shared network namespace, container relationship এবং lifecycle নিয়ে কাজ করব।
