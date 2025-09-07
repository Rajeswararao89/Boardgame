# BoardgameListingWebApp

## Description

**Board Game Database Full-Stack Web Application.**
This web application displays lists of board games and their reviews. While anyone can view the board game lists and reviews, they are required to log in to add/ edit the board games and their reviews. The 'users' have the authority to add board games to the list and add reviews, and the 'managers' have the authority to edit/ delete the reviews on top of the authorities of users.  

## Technologies

- Java
- Spring Boot
- Amazon Web Services(AWS) EC2
- Thymeleaf
- Thymeleaf Fragments
- HTML5
- CSS
- JavaScript
- Spring MVC
- JDBC
- H2 Database Engine (In-memory)
- JUnit test framework
- Spring Security
- Twitter Bootstrap
- Maven

## Features

- Full-Stack Application
- UI components created with Thymeleaf and styled with Twitter Bootstrap
- Authentication and authorization using Spring Security
  - Authentication by allowing the users to authenticate with a username and password
  - Authorization by granting different permissions based on the roles (non-members, users, and managers)
- Different roles (non-members, users, and managers) with varying levels of permissions
  - Non-members only can see the boardgame lists and reviews
  - Users can add board games and write reviews
  - Managers can edit and delete the reviews
- Deployed the application on AWS EC2
- JUnit test framework for unit testing
- Spring MVC best practices to segregate views, controllers, and database packages
- JDBC for database connectivity and interaction
- CRUD (Create, Read, Update, Delete) operations for managing data in the database
- Schema.sql file to customize the schema and input initial data
- Thymeleaf Fragments to reduce redundancy of repeating HTML elements (head, footer, navigation)

# Ultimate CI/CD Corporate DevOps Pipeline Project

## 🚀 Project Overview
This project demonstrates a real-time, end-to-end CI/CD pipeline solution designed to automate the complete software delivery lifecycle, from code commit to production deployment, using industry-standard DevOps tools and best practices.

---

## ✅ Key Features

- **Source Control Integration:**  
  Developers push code to **GitHub** repository triggering the pipeline.

- **Build & Unit Testing:**  
  Automated build process using **Maven** and unit test execution.

- **Code Quality Check:**  
  Static code analysis performed by **SonarQube** to maintain code quality.

- **Vulnerability Scanning:**  
  Comprehensive security scanning of source code and Docker images using **Aqua Trivy**.

- **Artifact Management:**  
  Build artifacts and Docker images are pushed to a private **Nexus Repository**.

- **Containerization:**  
  Application packaged as **Docker images**.

- **Kubernetes Deployment:**  
  Automated deployment of Docker containers into a scalable and fault-tolerant **Kubernetes Cluster**.

- **Security Compliance:**  
  Kubernetes cluster security scanned using **KubeAudit**.

- **Monitoring & Visualization:**  
  Real-time monitoring with **Prometheus & Grafana dashboards**.

- **Automated Notifications:**  
  Email alerts on successful or failed deployments.

---

## ⚙️ Technology Stack

| Category           | Tools & Technologies         |
|--------------------|-------------------------------|
| Source Control     | GitHub                        |
| CI/CD Tool         | Jenkins                       |
| Build Tool         | Maven                         |
| Code Quality Scan  | SonarQube                     |
| Vulnerability Scan | Aqua Trivy                    |
| Artifact Storage   | Nexus Repository              |
| Containerization   | Docker                        |
| Orchestration      | Kubernetes                    |
| Monitoring         | Prometheus, Grafana           |
| Notifications      | Gmail (SMTP integration)      |

---

## 🎯 Workflow Diagram

![Pipeline Architecture](a2bdc48e-e2c5-4943-80a2-e08024a54e31.png)

---

## 📋 Usage

1. Developer pushes code to the GitHub repository.
2. Jenkins pipeline is triggered:
    - Pulls the latest code.
    - Runs unit tests using Maven.
    - Performs SonarQube analysis.
    - Scans for vulnerabilities using Aqua Trivy.
    - Builds and tags Docker images.
    - Pushes images to Nexus Repository.
    - Pushes images to Docker Registry.
3. Deploy Docker images to Kubernetes.
4. Monitor services using Prometheus and Grafana.
5. Receive email notification upon successful deployment.

---













