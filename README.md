# TEST finzly-payment-galaxy platform
Main repo to host projects of payment galaxy product

Payment Galaxy
Payments run across disconnected systems—ACH in one platform, wires in another, instant payments somewhere else, and separate infrastructure for cross-border. Every new rail adds another integration, another compliance layer, and more operational strain.

Customers expect seamless, real-time payment experiences across every rail and currency. Your infrastructure shouldn’t be what slows that down.

# Finzly Payment Galaxy — CI/CD Pipeline

## About

This repository uses a fully automated **CI/CD pipeline** built with **GitHub Actions**, running on **self-hosted runners**, to build, test, secure-scan, containerize, and deploy the `galaxyapp` Java application to a **Kubernetes cluster (Amazon EKS)** on every push or pull request to the `main` branch.

The pipeline enforces a quality-gated release process — code only reaches production after it passes compilation, security scanning, unit testing, and static code analysis via SonarQube. Once validated, the application is packaged into a Docker image, pushed to Docker Hub, and rolled out to Kubernetes automatically.

### Pipeline stages

| Stage | Job | Description |
|---|---|---|
| 1️⃣ | **compile-job** | Checks out the code, sets up JDK 17 (Temurin), resolves Maven dependencies, and builds the project. |
| 2️⃣ | **security-check** | Scans the filesystem for vulnerabilities using **Trivy** and detects hardcoded secrets/credentials using **Gitleaks**. |
| 3️⃣ | **Test-phase** | Runs the project's unit test suite via Maven. |
| 4️⃣ | **build_and_sonarqube_phase** | Rebuilds the application, uploads the JAR as a build artifact, and runs static code analysis with **SonarQube**, enforcing a **Quality Gate** before allowing the pipeline to proceed. |
| 5️⃣ | **package_and_push_docker_image** | Downloads the built JAR, builds a multi-arch Docker image using **Buildx + QEMU**, and pushes it to **Docker Hub**. |
| 6️⃣ | **deploy_to_eks** | Connects to the **Amazon EKS** cluster, applies Kubernetes manifests, and rolls out the newly built image — completing the automated path from commit to running pod. |

### Deployment target — Kubernetes (Amazon EKS)

The final stage of the pipeline deploys the application to a **Kubernetes cluster hosted on Amazon EKS**. Each successful pipeline run:
- Builds and tags a Docker image
- Pushes it to `ramanan23/galaxyapp` on Docker Hub
- Updates the Kubernetes **Deployment** with the new image
- Exposes the application via a Kubernetes **Service**
- Verifies the rollout completes successfully before the pipeline is marked green

This ensures every change merged to `main` is automatically containerized and running live on Kubernetes — with no manual deployment steps required.

### Tech stack

- **CI/CD:** GitHub Actions (self-hosted runners)
- **Build:** Maven, JDK 17 (Temurin)
- **Security:** Trivy (filesystem scan), Gitleaks (secret detection)
- **Code Quality:** SonarQube
- **Containerization:** Docker, Buildx, QEMU
- **Registry:** Docker Hub
- **Orchestration:** Kubernetes on Amazon EKS



