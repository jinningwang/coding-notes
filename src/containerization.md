# Containerization

## 🧠 Images, Containers, and Pods

- 📦 **Images (The Blueprint)** A read-only template (like a recipe) containing the code, libraries, and environment needed to run an application.
- 🏃 **Containers (The Instance)** A live, running instance of an image (like the dish cooked from a recipe). Containers are isolated processes but share the host OS kernel.
- 🛸 **Pods (The Wrapper)** The smallest deployable unit in Kubernetes. It can hold one or more containers that must share: network (same IP), storage, and liftcycle.

### 📦 Docker Image

An **Image** is an immutable package.
In a Java workflow, an image typically bundles: 1) the `.jar` produced by Maven, 2) a Java runtime, 3) OS-level dependencies.

Key properties:

- **Immutability:** Once built, an image doesn't change. This ensures "it works on my machine" => "it works in production."
- **Layers:** Images are built in layers (OS -> Java -> App Code). If only application code changes, only the top layer is rebuilt. This sppeds up builds and recudes network transfer.

### 🛸 Pod vs. Container

In Kubernetes (K8s), you do **not** manage containers directly; you manage **Pods**.

Common patterns:

- **Single Container Pod** The most common case (1 Pod = 1 Java App).
- **Sidecar Pattern** A Pod with multiple containers (e.g., 1 Java App + 1 Log Collector). They live and die together, sharing the same IP address.
- **Resource Management (Pod Level)** You define hardware limits at the Pod level. Resources limits are defined on Pods:
    - `Requests`: The minimum guranted resources (CPU/RAM)
    - `Limits`: The maximum allowed resource

### 🏭 Node vs. Cluster

- **Node:** A single worker machine (Physical or VM) that runs Pods.
- **Cluster:** A group of Nodes managed as a single system. It provides:
  - scheduling
  - self-healing
  - high availability  

## 🛠️ The Automation Toolchain (CI/CD)

For an enterprise Java environment, these tools work together in a coordinated pipeline:

| Tool | Role | Function |
| :--- | :--- | :--- |
| **GitHub Ent.** | SCM (Source Control) | Stores the Java source code, Jenkinsfile, and Helm charts |
| **Maven** | Build Tool | Compiles code, runs tests, produces the `.jar` |
| **Jenkins** | Orchestrator | Drive the CI/CD workflow |
| **JFrog** | Artifact Manager | Stores JARs, Docker images, and Helm charts |
| **Helm** | K8s Package Manager | Packages and K8s configs |
| **Kubernetes** | Runtime Platform | Runs, scales, and heals Pods |

## 📜 Declarative Configuration

Modern systems use "code" to describe both:
- how things are run (process)
- what the system should look like (state)

### 🚀 Jenkinsfile (The Process)

Defines **how** the pipline runs.

Example responsibilities:
1. Pull code from GitHub
1. Run Maven build and tests
1. Build Docker image
1. Push image to JFrog
1. Deploy using Helm

Jenkinsfile answers:
> "In what order do steps happen?"

### ☸️ K8s YAML (The State)

Defines **what the system should look like**.

Examples:
- number of replicas
- container image version
- CPU/memory limits
- environment variables

Kubernetes continuously reconciles:
> **desired state** vs **actual state**

## 📦 Helm Charts (Declarative Packaging for Kubernetes)

In real-world Kubernetes systems, teams rarely deploy raw K8s YAML directly.

Instead, they use **Helm charts**, which add:
- structure
- templating
- versioning
- reuse

Helm sits *between* Jenkins and Kubernetes.

### 🎁 Helm Chart (The Package)

A **Helm chart** is a versioned bundle containing:

- Kubernetes YAML **templates**
- Default configuration **values**
- Metadata describing the application

It answers the question:

> “How do we deploy this application consistently across environments?”

Mental model:

> **Helm chart = Maven POM + application.yml + install script — for Kubernetes**

### 🧩 Helm vs. Raw Kubernetes YAML

| Aspect | Raw K8s YAML | Helm Chart |
|------|-------------|------------|
| Reusability | Low | High |
| Environment support | Copy & edit files | Values override |
| Versioning | Manual | Built-in |
| Rollback | Manual | One command |
| Enterprise usage | Rare | Standard |

🗂️ Helm Chart Structure (Conceptual)

```text
my-service/
├── Chart.yaml        # Metadata (name, version)
├── values.yaml       # Default config
├── values-prod.yaml  # Env overrides
└── templates/
    ├── deployment.yaml
    └── service.yaml
```

- **Templates** define structure
- **Values** supply environment-specific configuration

### 🔄 Helm’s Role in the CI/CD Flow

Helm does **not** replace Kubernetes and does **not** run inside the cluster.

Typical flow:
```text
Jenkinsfile
    ↓
helm upgrade --install
    ↓
Rendered Kubernetes YAML
    ↓
Kubernetes API Server
```

Kubernetes itself never sees the Helm charts — it only receives **plain YAML**.

## 🏡 Containers vs. Virtual Machines

Think of it as:
> **Renting a room** vs. **Buying a whole house**

| Feature | Containers (Docker/K8s) | Virtual Machines (VMware/VirtualBox) |
| :--- | :--- | :--- |
| **Isolation** | Process-level (shared kernel) | Hardware-level (guest OS) |
| **Size** | Lightweight (MBs) | Heavyweight (GBs) |
| **Startup** | Seconds | Minutes |
| **Efficiency** | High (Run hundreds on one host)| Lower (Resource overhead for each OS) |

### 🏘️ Virtual Machine: The Whole House
A VM is a full virtual computer:
- includes its own OS
- runs independently

**Pros**
- Strong isolation
- OS-level fault containment

**Cons**
- High resource cost
- Slow startup
- OS overhead per app

### 🏢 Container: The Apartment Complex

Containers share the host OS kernel but live in isolated environments.

**Pros**
- Fast startup
- High density
- Efficient resource usage

**Cons**
- Weaker isolation than VMs
- Kernel-level issues affect all containers
