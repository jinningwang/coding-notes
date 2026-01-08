# Containerization

## 🧠 Images, Containers, and Pods

- 📦 **Images (The Blueprint):** A read-only template (like a recipe) containing the code, libraries, and environment needed to run an application.
- 🏃 **Containers (The Instance):** A live, running instance of an image (like the dish cooked from a recipe). It is isolated but shares the host OS kernel.
- 🛸 **Pods (The Wrapper):** The smallest deployable unit in Kubernetes. It can hold one or more containers that need to share network and storage.

### 📦 Docker Image

An **Image** is an immutable (unchangeable) package.
In your Java workflow, it bundles the `.jar` file produced by Maven with an OpenJDK runtime.

- **Immutability:** Once built, an image doesn't change. This ensures "it works on my machine" translates to "it works in production."
- **Layers:** Images are built in layers (OS -> Java -> App Code). If you only change the code, only the top layer is rebuilt, saving time.

### 🛸 Pod vs. Container

In Kubernetes (K8s), you don't manage containers directly; you manage **Pods**.

- **Single Container Pod:** The most common case (1 Pod = 1 Java App).
- **Sidecar Pattern:** A Pod with multiple containers (e.g., 1 Java App + 1 Log Collector). They live and die together, sharing the same IP address.
- **Resource Management:** You define hardware limits at the Pod level.
    - `Requests`: The minimum resources (CPU/RAM) guaranteed to the Pod.
    - `Limits`: The maximum resources the Pod is allowed to consume.

### 🏭 Node vs. Cluster

- **Node:** A single worker machine (Physical or VM) that runs Pods.
- **Cluster:** A group of Nodes managed as a single system. It provides high availability—if one Node fails, the Cluster moves Pods to another.

## 🛠️ The Automation Toolchain (CI/CD)

For an enterprise Java environment, these "software" tools work together in a synchronized pipeline:

| Tool | Role | Function |
| :--- | :--- | :--- |
| **GitHub Ent.** | SCM (Source Control) | Stores the Java source code and `Jenkinsfile` |
| **Maven** | Build Tool | Compiles code, runs tests, and creates the `.jar` |
| **Jenkins** | Orchestrator | The "Commander" that triggers the workflow stages |
| **JFrog** | Artifact Manager | Stores the built `.jar` files and Docker Images |
| **Helm** | K8s Package Manager | Packages and deploys application configs |
| **Kubernetes** | Runtime Platform | The "Manager" that runs and scales the Pods |

## 📜 Declarative Configuration

Modern workflows use "Code" to describe "Infrastructure."

### 🚀 Jenkinsfile (The Script)
Defines the **Process**.
It tells Jenkins: "First pull from GitHub, then run Maven, then build the Docker image, and finally push to JFrog."

### ☸️ K8s YAML (The Manifest)
Defines the **State**.
It tells Kubernetes: "I want 3 replicas of this Java Pod running, using this specific image from JFrog, with a 1GB memory limit."

## 🏡 Containers vs. Virtual Machines

Understanding the difference between a **Container** and a **VM** is crucial for workflow automation.
Think of it as **Renting a Room** vs. **Buying a Whole House**.

| Feature | Containers (Docker/K8s) | Virtual Machines (VMware/VirtualBox) |
| :--- | :--- | :--- |
| **Isolation** | Process-level (Shares Host OS Kernel) | Hardware-level (Includes full Guest OS) |
| **Size** | Lightweight (MBs) | Heavyweight (GBs) |
| **Startup** | Seconds (Instant) | Minutes (Full Boot) |
| **Efficiency** | High (Run hundreds on one host) | Lower (Resource overhead for each OS) |

### 🏘️ Virtual Machine: The Whole House
A VM is a complete "virtual computer."
If you want to run a Java app, you must install a full operating system (Windows/Linux) inside the VM first.
- **Pros:** Extremely secure; if one VM's OS crashes, it doesn't affect others.
- **Cons:** High waste of resources; you "pay" for 5 operating systems just to run 5 apps.

### 🏢 Container: The Apartment Complex
Containers share the host's operating system but live in their own "private rooms."
- **Pros:** Fast and efficient. Since they share the "plumbing" (OS Kernel), they start instantly and use very little memory.
- **Cons:** Less isolation; if the host OS kernel has a critical failure, all containers on that host may be affected.

## 📦 Helm Charts (Declarative Packaging for Kubernetes)

In real-world Kubernetes systems, teams do not deploy raw K8s YAML directly.
They use Helm charts, which act as a packaging and configuration layer on top of Kubernetes.

### 🎁 Helm Chart (The Package)

A Helm chart is a versioned bundle of:
- Kubernetes YAML templates
- Default configuration values
- Metadata describing the application

It answers the question:

> “How do we deploy this application consistently across environments?”

Think of a Helm chart as:

> Maven POM + application.yml + install script — for Kubernetes

### 🧩 Helm vs. Raw K8s YAML

AspectRaw K8s YAMLHelm ChartReusabilityLowHighEnvironment supportCopy & edit filesValues overrideVersioningManualBuilt‑inRollbackManualOne commandEnterprise usageRareStandard

🗂️ Helm Chart Structure (Conceptual)

```bash
my-service/
├── Chart.yaml        # Metadata (name, version)
├── values.yaml       # Default config
├── values-prod.yaml  # Env overrides
└── templates/
    ├── deployment.yaml
    └── service.yaml
```

- **Templates** define structure
- **values** define environment‑specific configuration

### 🔄 Helm’s Role in the CI/CD Flow

Helm does not replace Kubernetes and does not run inside the cluster.

Instead:
1. Jenkins runs Helm
1. Helm renders templates into plain K8s YAML
1. Helm submits that YAML to the Kubernetes API

```bash
Jenkinsfile
   ↓
helm upgrade --install
   ↓
Rendered K8s YAML
   ↓
Kubernetes API
```

Kubernetes itself never sees the Helm chart — only final YAML.
