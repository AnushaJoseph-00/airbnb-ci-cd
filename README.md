# Airbnb Clone : CI/CD Pipeline on AWS

A full-stack Airbnb clone deployed end-to-end using a fully automated CI/CD pipeline on AWS. This project focuses on **DevOps implementation** - containerisation, infrastructure provisioning, secrets management, and continuous delivery.

> Built on top of the original application by [Sudeep Mahato](https://github.com/sudeepmahato16/airbnb-clone). All DevOps architecture and pipeline work by me.

---

## Pipeline Overview

```
GitHub → Jenkins → SonarQube → Docker → Amazon ECR → ECS Fargate → ALB
```

Code is pushed to GitHub, triggering a Jenkins webhook. Jenkins runs the build and dispatches a static analysis scan to SonarQube. On quality gate pass, Jenkins builds and tags a Docker image, pushes it to Amazon ECR, and deploys an updated ECS Fargate task behind an Application Load Balancer. Environment variables are configured directly in the ECS task definition - no credentials in the image or repository.

---

## Architecture Diagram

![CI/CD Architecture](architecture-diagram.png)

---

## DevOps Stack

| Layer | Tool / Service | Purpose |
|---|---|---|
| CI/CD Orchestration | Jenkins (on EC2) | Pipeline trigger, build, deploy |
| Code Quality | SonarQube (on EC2) | Static analysis, quality gate |
| Containerisation | Docker | Image build and packaging |
| Container Registry | Amazon ECR | Private image storage |
| Container Hosting | AWS ECS Fargate | Serverless task execution |
| Load Balancing | AWS ALB | HTTPS routing, health checks |
| Secrets Management | ECS Task Definition env vars | Runtime environment injection |
| Networking | AWS VPC, Security Groups | Traffic isolation and control |

---

## Application Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 14 + TypeScript |
| Database | MongoDB Atlas + Prisma ORM |
| Authentication | NextAuth (Google + GitHub OAuth) |
| Image Storage | EdgeStore |
| Maps | Leaflet |

---

## AWS Services Used

- **EC2** - hosts Jenkins and SonarQube on a single instance
- **ECR** - private Docker image registry, tagged per build
- **ECS Fargate** - serverless container runtime, no EC2 management
- **ALB** - application load balancer with health-check routing
- **VPC / Security Groups** - network segmentation and access control

---

## Pipeline in Action

### Jenkins pipeline — successful build
![Jenkins Pipeline](docs/screenshots/jenkins-pipeline-success.png)

### SonarQube code quality report
![SonarQube Report](docs/screenshots/sonarqube-report.png)

### Docker image pushed to Amazon ECR
![ECR Image](docs/screenshots/ecr-image-pushed.png)

### ECS Fargate service running
![ECS Service](docs/screenshots/ecs-service-running.png)

### ALB target group — healthy targets
![ALB Targets](docs/screenshots/alb-healthy-targets.png)

### Application live via ALB URL
![Live App](docs/screenshots/app-live.png)


---

## Environment Variables

In production, environment variables are configured directly in the ECS task definition via the AWS console - set when creating the task definition or updating the ECS service. They are injected into the running container at startup and never stored in the image or the repository.

For local development, create a `.env` file at the project root:

```env
DATABASE_URL=
GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
NEXTAUTH_SECRET=
NEXTAUTH_URL=
EDGE_STORE_ACCESS_KEY=
EDGE_STORE_SECRET_KEY=
```

> Never commit `.env` to source control. Add it to `.gitignore`.

---

## How to Run Locally

```bash
git clone https://github.com/AnushaJoseph-00/airbnb-clone-ci-cd.git
cd airbnb-clone-ci-cd
cp .env.example .env       # fill in your values
npm install
npx prisma generate
npm run dev
```

---

## Author

**Anusha Joseph**
GitHub: [@AnushaJoseph-00](https://github.com/AnushaJoseph-00)

---

## Credits

Original application by [Sudeep Mahato](https://github.com/sudeepmahato16) — [sudeepmahato16/airbnb-clone](https://github.com/sudeepmahato16/airbnb-clone).

This repository covers the DevOps layer: CI/CD pipeline, containerisation, and AWS deployment built on top of the original codebase.
