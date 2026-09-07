# eCommerce Multibranch GitOps Deployment with ArgoCD

##  Project Overview
This project establishes a production-grade GitOps CI/CD pipeline to automate the delivery of a Python Flask-based eCommerce application. By utilizing Jenkins for Continuous Integration and ArgoCD for Continuous Deployment, the architecture ensures seamless, zero-downtime feature rollouts directly to an Amazon EKS cluster[cite: 1, 2]. 

## Tech Stack
* **Application Layer:** Python 3, Flask (`app.py`)[cite: 2]
* **Infrastructure as Code (IaC):** Terraform (AWS EKS, EC2 provisioning)[cite: 2]
* **Containerization:** Docker, Docker Hub[cite: 1, 2]
* **Continuous Integration (CI):** Jenkins (Multibranch Pipeline)[cite: 1, 2]
* **GitOps / CD:** ArgoCD[cite: 1, 2]
* **Container Orchestration:** Amazon Elastic Kubernetes Service (EKS)
* **Observability:** Prometheus & Grafana (deployed via Helm)

##  Architecture & GitOps Workflow
1. **Development:** Developers build new features in isolated branches (e.g., `featA`, `featB`) and raise a Pull Request against the `main` branch.
2. **Continuous Integration:** Upon merging the PR, Jenkins triggers the `Jenkinsfile`[cite: 2]. It builds a new Docker image, pushes it to the registry, and programmatically updates the image tag inside `k8s/deployment.yaml`[cite: 2].
3. **Continuous Deployment:** ArgoCD continuously monitors the `k8s/` directory exclusively on the `main` branch[cite: 1, 2]. Upon detecting the updated deployment manifest, ArgoCD automatically syncs the EKS cluster to the desired state, rolling out the new pods[cite: 1, 2].

##  Repository Structure
```text
ecommerce-gitops-pipeline/
│
├── app.py                  # Core Flask application[cite: 2]
├── requirements.txt        # Python dependencies[cite: 2]
├── Dockerfile              # Container build instructions[cite: 2]
├── Jenkinsfile             # CI pipeline configuration[cite: 2]
│
├── Tf-script/              # Terraform IaC configurations[cite: 2]
│   ├── Main.tf            [cite: 2]
│   ├── provider.tf        [cite: 2]
│   └── resource.sh        [cite: 2]
│
├── k8s/                    # Kubernetes manifests (Monitored by ArgoCD)[cite: 1, 2]
│   ├── deployment.yaml    [cite: 2]
│   └── service.yaml       [cite: 2]
│
└── argocd/                 # ArgoCD application definitions
    └── application.yaml
