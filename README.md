# Kubernetes টিউটোরিয়াল

Kubernetes শেখার একটি হাতে-কলমে যাত্রা। Kubernetes কেন তৈরি হয়েছে, এটি ভেতরে কীভাবে কাজ করে এবং কীভাবে এর সাহায্যে application পরিচালনা করতে হয়, ধাপে ধাপে শিখব।

এই repository শুধু YAML উদাহরণ বা command-এর সংগ্রহ নয়। এটি একটি পূর্ণাঙ্গ learning book। আমাদের লক্ষ্য Kubernetes-এর mental model তৈরি করা: এটি কোন সমস্যার সমাধান করে, কীভাবে করে, এর design trade-off কী এবং কোনো কিছু ভুল হলে কীভাবে তা অনুসন্ধান করতে হয়।

## শেখার লক্ষ্য

এই শেখার যাত্রা শেষে তুমি যেন:

- Kubernetes-এর গুরুত্বপূর্ণ concept এবং সেগুলো কেন প্রয়োজন, নিজের ভাষায় ব্যাখ্যা করতে পারো।
- Kubernetes-এ application deploy, inspect, scale, update এবং troubleshoot করতে পারো।
- বিচ্ছিন্ন configuration হিসেবে নয়, বরং Kubernetes resource-গুলোর পারস্পরিক সম্পর্ক বুঝতে পারো।
- Scheduling, networking, storage, health, security এবং failure behavior নিয়ে যুক্তিসংগতভাবে চিন্তা করতে পারো।
- API server, controller, reconciliation, watch এবং etcd-এর মতো গুরুত্বপূর্ণ internal mechanism সম্পর্কে বুঝতে পারো।
- অন্ধভাবে configuration copy না করে, বাস্তব পরিস্থিতি অনুযায়ী সিদ্ধান্ত নিতে পারো।

## আমরা কীভাবে শিখব

প্রতিটি বিষয় intuition থেকে শুরু করে implementation পর্যন্ত নিয়ে যাব। এরপর experiment-এর মাধ্যমে বোঝাপড়া যাচাই করব।

1. **Concept:** সমস্যাটি কী, কেন তৈরি হয়, প্রয়োজনীয় terminology এবং mental model।
2. **Visualization:** Diagram, request flow, timeline ও resource-গুলোর সম্পর্ক। প্রয়োজন অনুযায়ী Mermaid ব্যবহার করা হবে।
3. **Under the hood:** Kubernetes-এর component-গুলো কীভাবে একে অপরের সঙ্গে কাজ করে এবং ভেতরে কী ঘটে।
4. **Hands-on lab:** Local Kubernetes cluster-এ ধারণাটি বাস্তবে প্রয়োগ করা।
5. **Experiment ও observation:** Event, status, log পর্যবেক্ষণ করা এবং একবারে একটি variable পরিবর্তন করে ফলাফল দেখা।
6. **Failure analysis:** নিরাপদভাবে কোনো কিছুতে failure তৈরি করা, সেটি diagnose করা এবং Kubernetes-এর recovery-র সীমাবদ্ধতা বোঝা।
7. **Checkpoint:** শেখা বিষয়গুলো যাচাই করার জন্য প্রশ্ন বা ছোট task।

YAML ও command হলো Kubernetes-এর behavior প্রকাশ ও পর্যবেক্ষণ করার মাধ্যম, শেখার চূড়ান্ত লক্ষ্য নয়।

## Chapter Roadmap

| Chapter | বিষয় | কী শিখব |
|---|---|---|
| 00 | [Kubernetes-এর ভিত্তি](chapters/00-kubernetes-foundation/README.md) | Application বড় হওয়ার সঙ্গে সঙ্গে পরিচালনার সমস্যা; container; orchestration কেন প্রয়োজন; Kubernetes কী এবং কী নয়। |
| 01 | [Kubernetes Architecture](chapters/01-kubernetes-architecture/README.md) | Cluster, node, control plane, worker node, প্রধান component এবং তারা কীভাবে একসঙ্গে কাজ করে। |
| 02 | [Pods](chapters/02-pods/README.md) | Pod কেন আছে; গঠন, lifecycle, networking-এর ভিত্তি, multi-container pattern এবং সীমাবদ্ধতা। |
| 03 | Workloads: Deployment ও ReplicaSet | Desired replica, declarative management, rollout, scaling, self-healing এবং ReplicaSet-এর ভূমিকা। |
| 04 | Kubernetes Networking | Pod networking, Service, selector, endpoint, DNS, ClusterIP, NodePort এবং Ingress/Gateway-এর পরিচিতি। |
| 05 | Configuration ও Secrets | ConfigMap, Secret, environment variable, mounted file, update behavior এবং security সতর্কতা। |
| 06 | Storage ও Persistence | Volume, PV, PVC, StorageClass, lifecycle এবং container filesystem-এর বাইরে data সংরক্ষণ। |
| 07 | Scheduling ও Resource Management | Scheduler, request/limit, capacity, scheduling decision, affinity/anti-affinity, taint, toleration এবং resource pressure। |
| 08 | Application Health ও নিরাপদ Deployment | Startup/readiness/liveness probe, rolling update, rollout status, rollback এবং health check-এর মাধ্যমে traffic ও recovery নিয়ন্ত্রণ। |
| 09 | Kubernetes Security-এর ভিত্তি | Authentication ও authorization-এর পার্থক্য, RBAC, ServiceAccount, security context, least privilege এবং সাধারণ ভুল। |
| 10 | Kubernetes Internals | API server, etcd, controller, reconciliation loop, watch, desired state বনাম actual state এবং change propagation। |
| 11 | Observability ও Troubleshooting | Event, log, resource inspection, metrics-এর ধারণা, সাধারণ failure state এবং systematic debugging। |
| 12 | Production Practices | Deployment strategy, resource planning, reliability, operational trade-off এবং production-oriented design। |
| 13 | Capstone: একটি Application Deploy ও পরিচালনা | একটি বাস্তবসম্মত ছোট application deploy, configure, expose, scale, update, break, diagnose এবং recover করা। |

### Roadmap সম্পর্কে কিছু কথা

- এটি শেখার একটি ধারাবাহিক ক্রম, Kubernetes-এর প্রতিটি feature শেখানোর প্রতিশ্রুতি নয়।
- Helm, custom resource, operator, autoscaling এবং advanced networking প্রয়োজন অনুযায়ী পরিচয় করানো হবে অথবা আলাদা extension হিসেবে যোগ হবে।
- Kubernetes-এর নিজস্ব behavior এবং add-on, cloud provider বা third-party tool-এর behavior আলাদা করে চিহ্নিত করা হবে।
- Capstone-এ নতুন নতুন unrelated tool না এনে আগের chapter-গুলোর ধারণা একত্রে ব্যবহার করা হবে।

## প্রতিটি Chapter-এর কাঠামো

প্রতিটি chapter মোটামুটি একই কাঠামো অনুসরণ করবে। বিষয় অনুযায়ী কোনো section বাদ বা বড় করা হতে পারে।

1. **কেন এটি প্রয়োজন?** — একটি বাস্তব সমস্যা এবং সেটি সমাধানে এই mechanism কেন দরকার।
2. **Mental Model** — Terminology ও configuration-এর আগে সহজবোধ্য ধারণা তৈরি।
3. **কীভাবে কাজ করে?** — দায়িত্ব, গুরুত্বপূর্ণ concept, resource-গুলোর সম্পর্ক এবং behavior।
4. **Visualization** — প্রয়োজন অনুযায়ী architecture, sequence, flow, state বা relationship diagram।
5. **Under the Hood** — Component interaction, API behavior, scheduling, networking, state transition অথবা Linux/container mechanics।
6. **Hands-on Lab** — ব্যাখ্যাসহ command ও manifest ব্যবহার করে ছোট ছোট practical exercise।
7. **পর্যবেক্ষণ ও Experiment** — Status, event ও log দেখা; configuration পরিবর্তন করে ফলাফল যাচাই।
8. **Failure Analysis** — নিয়ন্ত্রিত failure তৈরি, diagnose করা এবং Kubernetes কীভাবে recover করে বা কোথায় পারে না তা বোঝা।
9. **Key Takeaways** — গুরুত্বপূর্ণ mental model ও প্রচলিত ভুল ধারণার সারাংশ।
10. **Checkpoint** — শুধু মুখস্থ নয়, সত্যিকারের বোঝাপড়া যাচাই করার প্রশ্ন বা task।

## শেখার Environment

Cloud account বা paid infrastructure ছাড়াই যাতে experiment করা যায়, সে জন্য আমরা local Kubernetes cluster ব্যবহার করব।

| Tool | কেন ব্যবহার করব |
|---|---|
| Docker | Container চালানো এবং local cluster-এর node environment তৈরি করা। |
| Kind (Kubernetes in Docker) | Docker container-কে node হিসেবে ব্যবহার করে local Kubernetes cluster তৈরি করা। |
| kubectl | Kubernetes API-এর মাধ্যমে resource inspect ও manage করা। |
| Terminal | Command চালানো এবং output ও failure অনুসন্ধান করা। |
| Git | Tutorial content, manifest এবং lab-এর পরিবর্তন track করা। |

কোনো topic-এর জন্য সত্যিই প্রয়োজন না হলে অতিরিক্ত tool যোগ করব না।

### Environment Setup-এর ধাপ

1. Container environment এবং basic container workflow যাচাই।
2. প্রয়োজনীয় local Kubernetes tool install ও verify করা।
3. Kind ব্যবহার করে local cluster তৈরি করা।
4. kubectl-কে cluster-এর সঙ্গে configure করা।
5. Cluster verify করা এবং node inspect করা।
6. পরবর্তী lab-গুলোতে একই environment ব্যবহার করা।
7. প্রয়োজন অনুযায়ী resource ও cluster cleanup করা।

Installation এবং troubleshooting-এর বিস্তারিত নির্দেশনা সংশ্লিষ্ট setup chapter বা lab-এ যোগ করা হবে।

## Repository-এর কাঠামো

Chapter ধরে ধরে tutorial বড় হবে। প্রস্তাবিত কাঠামো:

```text
k8s-tutorial/
├── README.md
└── chapters/
    ├── 00-kubernetes-foundation/
    │   ├── README.md
    │   ├── diagrams/
    │   └── labs/
    ├── 01-kubernetes-architecture/
    │   ├── README.md
    │   ├── diagrams/
    │   └── labs/
    ├── 02-pods/
    │   ├── README.md
    │   ├── diagrams/
    │   └── labs/
    └── ...
```

প্রতিটি chapter-এর জন্য সব folder প্রয়োজন হবে না। কনটেন্টের কাজে লাগলেই নতুন file বা folder যোগ করব। শুধু কাঠামো দেখানোর জন্য খালি folder তৈরি করব না।

## শেখার Workflow

আমাদের শেখার দুটি জায়গা থাকবে: ChatGPT হবে interactive classroom ও lab companion, আর repository হবে দীর্ঘমেয়াদি reference book।

1. **এখানে chapter শুরু:** মূলত বাংলায় conversational lesson হবে। প্রয়োজনীয় Kubernetes terminology ইংরেজিতেই রাখা হবে।
2. **বিষয়টি নিয়ে আলোচনা:** প্রশ্ন করা, বিকল্প ব্যাখ্যা দেখা এবং অস্পষ্টতা দূর করা।
3. **Visualization:** Diagram, flow ও বাস্তব উদাহরণ দিয়ে mental model তৈরি।
4. **Lab চালানো:** নিজের machine-এ command execute করে cluster-এর প্রকৃত অবস্থা দেখা।
5. **Experiment:** কোনো পরিবর্তনের পর কী ঘটবে তা আগে অনুমান করা, তারপর পরীক্ষা করে দেখা।
6. **Failure অনুসন্ধান:** ইচ্ছাকৃতভাবে তৈরি করা সমস্যা evidence-এর ভিত্তিতে diagnose করা।
7. **Chapter চূড়ান্ত করা:** বিষয়টি ভালোভাবে বোঝা ও আলোচনা শেষ হলে সেটিকে পরিপাটি tutorial হিসেবে repository-তে সংরক্ষণ করা।
8. **Review ও পরবর্তী অধ্যায়:** Chapter-গুলোর মধ্যে link রাখা এবং নতুন বিষয় শেখার সময় আগের ধারণাগুলো আরও গভীরভাবে দেখা।

অর্থাৎ, Conversation হবে interactive classroom; repository হবে আমাদের স্থায়ী reference।

## Prerequisites

Kubernetes সম্পর্কে আগে থেকে কিছু জানা বাধ্যতামূলক নয়। তবে নিচের বিষয়গুলো জানা থাকলে শেখা সহজ হবে:

- Command line-এর সাধারণ ব্যবহার।
- Software development-এর মৌলিক ধারণা।
- Networking-এর প্রাথমিক ধারণা (IP address, port, HTTP)। প্রয়োজন অনুযায়ী এগুলোও ব্যাখ্যা করা হবে।
- Docker ও container সম্পর্কে সামান্য পরিচিতি। প্রয়োজনীয় অংশগুলো আগে আলোচনা করব।

Kubernetes internals, cluster administration বা cloud infrastructure সম্পর্কে পূর্ব অভিজ্ঞতা ধরে নেওয়া হবে না।

## আমাদের শেখার নীতিমালা

- **মুখস্থ নয়, বুঝে শেখা:** প্রতিটি resource বা setting কেন আছে তা ব্যাখ্যা করা।
- **অদৃশ্য behavior দৃশ্যমান করা:** Diagram, status inspection, event এবং experiment ব্যবহার করা।
- **নিজে করে শেখা:** গুরুত্বপূর্ণ concept-এর সঙ্গে practical exercise রাখা।
- **শুধু সফলতা নয়, failure-ও শেখা:** Recovery mechanism এবং তার সীমাবদ্ধতা বোঝা।
- **Core ও extension আলাদা রাখা:** Kubernetes-এর নিজস্ব behavior এবং vendor, add-on বা external tool-এর behavior আলাদা করে চেনা।
- **ধাপে ধাপে এগোনো:** সম্পর্কহীন topic-এ লাফ না দিয়ে আগের ধারণা কাজে লাগানো।
- **বাস্তবসম্মত থাকা:** অপ্রয়োজনীয় complexity ছাড়াই বাস্তব application operation-এর scenario ব্যবহার করা।

---

## এখান থেকে শুরু করো

শুরু করো **Chapter 00: Kubernetes-এর ভিত্তি** দিয়ে। Kubernetes resource শেখার আগে আমরা বুঝব Kubernetes কোন operational problem সমাধান করতে তৈরি হয়েছে এবং পরবর্তী chapter-গুলোর জন্য প্রয়োজনীয় container foundation তৈরি করব।
