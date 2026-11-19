# Kubernetes Tutorial

A hands-on journey from understanding why Kubernetes exists to understanding how it works under the hood and how to operate applications on it.

This repository is a learning book, not a collection of YAML recipes. The goal is to build a mental model of Kubernetes: the problems it solves, the mechanisms behind its behavior, the trade-offs it makes, and how to investigate it when things go wrong.

## Learning Goals

By the end of this journey, you should be able to:
- Explain Kubernetes concepts and the problems they solve.
- Deploy, inspect, scale, update, and troubleshoot applications on Kubernetes.
- Understand resource relationships rather than treating manifests as isolated configuration.
- Reason about scheduling, networking, storage, health, security, and failure behavior.
- Understand core internals such as the API server, controllers, reconciliation, watches, and etcd.
- Make informed operational decisions instead of blindly copying configurations.

## How We Learn

Each topic moves from intuition to implementation, then verifies understanding through experiments:

1. **Concept:** Problem, motivation, terminology, and mental model.
2. **Visualization:** Diagrams, request flows, timelines, and relationships (Mermaid where useful).
3. **Under the hood:** Component interactions and implementation details.
4. **Hands-on lab:** Apply the concept in a local cluster.
5. **Experiment and observe:** Inspect events, status, logs, and change one variable at a time.
6. **Failure analysis:** Safely break something, diagnose it, and learn Kubernetes' recovery boundaries.
7. **Checkpoint:** Questions or small tasks to verify understanding.

YAML and commands are tools for expressing and observing Kubernetes behavior, not the end goal.

## Chapter Roadmap

| Chapter | Topic | What We'll Learn |
|---|---|---|
| 00 | Kubernetes Foundation | Application operations as they grow; containers; why orchestration is needed; what Kubernetes is and isn't. |
| 01 | Kubernetes Architecture | Cluster, nodes, control plane, worker nodes, major components, and how they coordinate. |
| 02 | Pods | Why Pods exist; structure, lifecycle, networking basics, multi-container patterns, and limitations. |
| 03 | Workloads: Deployment and ReplicaSet | Desired replicas, declarative management, rollouts, scaling, self-healing, and ReplicaSet's role. |
| 04 | Kubernetes Networking | Pod networking, Services, selectors, endpoints, DNS, ClusterIP, NodePort, and introduction to Ingress/Gateway. |
| 05 | Configuration and Secrets | ConfigMaps, Secrets, environment variables, mounted files, update behavior, and security caveats. |
| 06 | Storage and Persistence | Volumes, PVs, PVCs, StorageClasses, lifecycle, and persistence beyond container filesystems. |
| 07 | Scheduling and Resource Management | Scheduler, requests/limits, capacity, scheduling decisions, affinity/anti-affinity, taints, tolerations, and resource pressure. |
| 08 | Application Health and Deployment Safety | Startup/readiness/liveness probes, rolling updates, rollout status, rollback, and health-driven traffic/recovery. |
| 09 | Kubernetes Security Fundamentals | Authentication vs authorization, RBAC, ServiceAccounts, security contexts, least privilege, and common mistakes. |
| 10 | Kubernetes Internals | API server, etcd, controllers, reconciliation loops, watches, desired vs actual state, and change propagation. |
| 11 | Observability and Troubleshooting | Events, logs, resource inspection, metrics concepts, common failure states, and systematic debugging. |
| 12 | Production Practices | Deployment strategies, resource planning, reliability, operational trade-offs, and production-oriented design. |
| 13 | Capstone: Deploy and Operate an Application | Deploy, configure, expose, scale, update, break, diagnose, and recover a realistic small application. |

### Roadmap Notes

- This is a learning sequence, not a promise to cover every Kubernetes feature.
- Helm, custom resources, operators, autoscaling, and advanced networking may be introduced when they fit naturally or added as focused extensions.
- We will distinguish Kubernetes core behavior from add-ons, cloud-provider behavior, and third-party tools.
- The capstone will reuse earlier concepts instead of introducing unrelated tools.

## Chapter Structure

Chapters will follow this structure, expanding or omitting sections when appropriate:

1. **Why This Exists** — A concrete problem and why a mechanism is needed.
2. **Mental Model** — An intuitive model before terminology and configuration.
3. **How It Works** — Responsibilities, key concepts, relationships, and behavior.
4. **Visualization** — Architecture, sequence, flow, state, or relationship diagrams as useful.
5. **Under the Hood** — Component interactions, API behavior, scheduling, networking, state transitions, or Linux/container mechanics.
6. **Hands-on Lab** — Focused exercises with explained commands and manifests.
7. **Observe and Experiment** — Inspect status/events/logs; change inputs and verify outcomes.
8. **Failure Analysis** — Introduce a controlled failure, diagnose it, and understand Kubernetes' recovery limits.
9. **Key Takeaways** — Essential mental models and misconceptions.
10. **Checkpoint** — Questions or tasks that test understanding rather than recall.

## Learning Environment

We will use a local cluster to keep experiments reproducible without cloud accounts or paid infrastructure.

| Tool | Purpose |
|---|---|
| Docker | Run containers and provide the container environment for our local cluster. |
| Kind (Kubernetes in Docker) | Run local Kubernetes clusters with Docker containers acting as nodes. |
| kubectl | Inspect and manage Kubernetes resources through the Kubernetes API. |
| Terminal | Execute commands and investigate output/failures. |
| Git | Track tutorial material, manifests, and lab changes. |

We may introduce extra tools later only when a topic genuinely needs them.

### Environment Setup Progression

1. Verify the container environment and basic container workflow.
2. Install and verify local Kubernetes tools.
3. Create a local cluster using Kind.
4. Configure kubectl to communicate with it.
5. Verify the cluster and inspect its nodes.
6. Reuse this environment throughout labs.
7. Clean up resources/clusters when appropriate.

Detailed installation and troubleshooting instructions will live in the relevant setup chapter or lab.

## Repository Structure

The tutorial will grow chapter by chapter:

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

Not every chapter needs every folder. We will add structure when it serves the content rather than create empty scaffolding.

## Study Workflow

The conversation is the interactive classroom and lab companion; the repository is the durable reference.

1. **Start a chapter here:** Lessons are taught conversationally, primarily in Bengali, with English Kubernetes terminology retained where useful.
2. **Explore:** Ask questions and clarify gaps before moving on.
3. **Visualize:** Use diagrams, flows, and concrete examples.
4. **Run labs:** Execute commands locally and inspect actual cluster state.
5. **Experiment:** Predict the outcome of a change, then verify it.
6. **Investigate failures:** Diagnose intentionally introduced issues using evidence.
7. **Finalize:** Once the lesson is settled, turn it into a polished English chapter in this repository.
8. **Review and continue:** Link chapters and revisit earlier concepts as new topics deepen them.

## Prerequisites

No Kubernetes experience is required. Helpful foundations:
- Basic command-line usage
- Fundamental software development concepts
- Basic networking (IP addresses, ports, HTTP), introduced as needed
- Basic Docker/container familiarity (we will review relevant concepts before depending on them)

We will not assume prior knowledge of Kubernetes internals, cluster administration, or cloud infrastructure.

## Guiding Principles

- **Understand before memorizing:** Explain why each resource or setting exists.
- **Make invisible behavior visible:** Use diagrams, status inspection, events, and experiments.
- **Learn by doing:** Major concepts should include practical exercises.
- **Study failure, not just success:** Understand recovery mechanisms and their limits.
- **Separate core from extensions:** Identify Kubernetes behavior versus vendors, add-ons, and external tools.
- **Build progressively:** Reuse concepts rather than jumping between unrelated topics.
- **Keep it realistic:** Use practical application scenarios without unnecessary complexity.

## Start Here

Begin with **Chapter 00: Kubernetes Foundation**. Before learning Kubernetes resources, we will understand the operational problems Kubernetes solves and establish the container foundation needed for the rest of the tutorial.
