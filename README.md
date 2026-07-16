# Airbnb Clone - CI/CD Pipeline on AWS

A full-stack Airbnb clone application deployed using a 
complete CI/CD pipeline on AWS cloud infrastructure.

##  Project Overview

This project demonstrates end-to-end DevOps practices by 
taking an existing full-stack application and building a 
complete automated CI/CD pipeline around it.

##  Application Stack
- **Framework:** Next.js 14 + TypeScript
- **Styling:** Tailwind CSS
- **Database:** MongoDB Atlas + Prisma ORM
- **Authentication:** NextAuth (Google + GitHub OAuth)
- **Image Storage:** EdgeStore
- **Maps:** Leaflet

## DevOps Stack
- **CI/CD:** Jenkins
- **Code Quality:** SonarQube
- **Containerisation:** Docker
- **Container Registry:** AWS ECR
- **Hosting:** AWS ECS Fargate
- **Load Balancer:** AWS ALB

##  Pipeline Flow

##  AWS Services Used
- EC2 (Jenkins + SonarQube server)
- ECR (Container Registry)
- ECS Fargate (Serverless container hosting)
- ALB (Application Load Balancer)
- SSM Parameter Store (Secrets management)


## Architecture Diagram
[architecture diagram here]


##  Live URL
[ALB URL here after deployment]

##  Features
- User registration and authentication (Google + GitHub)
- Property listing and browsing
- Property booking and reservations
- Search and filtering of properties
- Interactive map using Leaflet
- Image uploads via EdgeStore

## 📝 Environment Variables
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

## Author
Anusha Joseph
- GitHub: [@AnushaJoseph-00](https://github.com/AnushaJoseph-00)

## Credits

Original application developed by 
[Sudeep Mahato](https://github.com/sudeepmahato16).

Original repository: 
[sudeepmahato16/airbnb-clone](https://github.com/sudeepmahato16/airbnb-clone)

This project focuses on the **DevOps implementation** CI/CD pipeline, containerisation, and AWS deployment built on top of the original application.
