# LibraSys Services 📚

**Student Name:** Seenathul Ilma  
**Student Number:** 241711005  
**Slack Handle:** Seenathul Ilma Musawwir  
**GCP Project ID:** librasys-eca  

---

The core business logic of the LibraSys microservices architecture. This repository acts as a "Super Repo" managing the domain services using Git Submodules.

## Architecture

This repository contains the following domain microservices:

- **User Service (Port 8081):** Manages library members. Connects to **Google Cloud SQL** (MySQL).
- **Book Service (Port 8082):** Manages the library catalog. Connects to **MongoDB** and uses **Google Cloud Storage (GCS)** for book cover images.
- **Loan Service (Port 8083):** Manages borrowing and returning operations. Connects to **Google Cloud SQL** (MySQL).

## Technologies Used
- Java 25
- Spring Boot 3.4.2
- Spring Data JPA (for MySQL / Cloud SQL)
- Spring Data MongoDB
- Google Cloud Storage SDK

## GCP Deployment Architecture
- **Compute Engine (VM):** All services run on a centralized Debian VM (`librasys-backend`) managed by PM2.
- **Databases:**
  - User & Loan data: **Google Cloud SQL** (MySQL) — managed relational database
  - Book data: **MongoDB** — hosted on the VM (NoSQL)
- **Object Storage:** Book cover images are uploaded to a **Google Cloud Storage (GCS)** bucket (`librasys-book-covers`).

## How to Run Locally
*Note: Ensure the `librasys-platform` services (Config Server, Eureka, Gateway) are running first.*
1. Configure your local `application.yml` or ensure the Config Server is reachable at `localhost:8888`.
2. Start the `user-service`, `book-service`, and `loan-service` via Maven (`./mvnw spring-boot:run`) or your IDE.
