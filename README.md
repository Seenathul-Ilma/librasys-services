# LibraSys Services- Enterprise Cloud Architecture 📚

**Student Name:** Seenathul Ilma  
**Student Number:** 241711005  
**Slack Handle:** Seenathul Ilma Musawwir  
**GCP Project ID:** `librasys-eca`

---

## 📖 Overview

`librasys-services` is the **business logic layer** of the LibraSys Library Management System. It is a polyrepo super-repo bundling the three domain microservices that implement the actual library operations: managing members, the book catalogue, and borrow/return transactions.

| Submodule | Domain | Port | Persistence |
|---|---|---|---|
| [`user-service`](https://github.com/Seenathul-Ilma/librasys-user-service) | Library members | `8081` | Cloud SQL (MySQL) |
| [`book-service`](https://github.com/Seenathul-Ilma/librasys-book-service) | Book catalogue + cover images | `8082` | MongoDB + Cloud Storage (GCS) |
| [`loan-service`](https://github.com/Seenathul-Ilma/librasys-loan-service) | Borrow / return transactions | `8083` | Cloud SQL (MySQL) + Firestore audit log |

All three register themselves with **Eureka** and receive their configuration from the **Config Server** at boot, both provided by the sibling repo **[`librasys-platform`](https://github.com/Seenathul-Ilma/librasys-platform)**. The UI that calls these services lives in **[`librasys-frontend`](https://github.com/Seenathul-Ilma/librasys-frontend)**.

---

## 🏗️ Architecture Breakdown

Each service owns its own datastore (database-per-service pattern) and is only reachable through the API Gateway — there is no direct service-to-service HTTP coupling in the frontend flow.

```
        API Gateway (8080)
               │
   ┌───────────┼───────────────┐
   ▼           ▼               ▼
user-service book-service   loan-service
  (8081)       (8082)          (8083)
   │             │                │
   ▼             ▼                ▼
Cloud SQL    MongoDB (local    Cloud SQL
(MySQL)      to VM) + GCS       (MySQL)
librasys_    bucket             librasys_
user_        librasys-book-     loan_
service_db   covers             service_db
                                    │
                                    ▼
                              Firestore
                            loan-audit-log
                        (BORROWED / RETURNED events)
```

- **user-service** — CRUD for members (`name`, `email`, `phone`, `memberType`), backed by Cloud SQL MySQL.
- **book-service** — CRUD for the catalogue; book metadata is stored in MongoDB, and cover images are uploaded straight to the `librasys-book-covers` GCS bucket, with the public object URL saved back on the book document.
- **loan-service** — Issues and closes loans (`BORROWED` / `RETURNED`) against Cloud SQL, and on every state change writes an audit document to the `loan-audit-log` Firestore collection — demonstrating a second, independent NoSQL store beyond MongoDB.

---

## 🧰 Tech Stack

| Layer | Technology |
|---|---|
| Framework | Spring Boot 3.4.2, Java 25 |
| Relational data | Spring Data JPA + Cloud SQL (MySQL 8.0) |
| Document data | Spring Data MongoDB |
| Object storage | Spring Cloud GCP Storage starter → GCS |
| Audit log | Google Cloud Firestore client library |
| Service discovery | Netflix Eureka client |
| Config | Spring Cloud Config client |
| Build tool | Maven |
| Boilerplate | Lombok |

---

## ⚙️ Setup & Local Run

### Clone with submodules
```bash
git clone --recurse-submodules https://github.com/Seenathul-Ilma/librasys-services.git
cd librasys-services
```

### Build (any order — each is independent)
```bash
cd user-service && mvn clean package -DskipTests && cd ..
cd book-service && mvn clean package -DskipTests && cd ..
cd loan-service && mvn clean package -DskipTests && cd ..
```

### Run locally
> Requires `config-server` (8888) and `service-registry` (8761) from `librasys-platform` already running.

```bash
java -jar user-service/target/user-service-*.jar   # http://localhost:8081
java -jar book-service/target/book-service-*.jar   # http://localhost:8082
java -jar loan-service/target/loan-service-*.jar   # http://localhost:8083
```

### Local prerequisites
- MySQL running locally, or point `user-service.yaml` / `loan-service.yaml` (in `config-server`) at your Cloud SQL public IP.
- MongoDB running locally on `27017`, or point `book-service.yaml` at a remote instance.
- A GCP service account key or Application Default Credentials for GCS/Firestore access.

---

## ☁️ Live Deployment

| Item | Value |
|---|---|
| GCP Project | `librasys-eca` (`us-central1` / `us-central1-a`) |
| Compute | Managed Instance Group, all 3 services run under PM2 alongside the platform services |
| API base (backend) | `https://librasys-eca.duckdns.org` |
| Cloud SQL instance | `librasys-mysql-db` (`librasys_user_service_db`, `librasys_loan_service_db`) |
| GCS bucket | `gs://librasys-book-covers` |
| Firestore | `loan-audit-log` collection |
| Frontend (Cloud Run) | https://librasys-frontend-529440660281.us-central1.run.app |

---

## 🔗 Related Repositories
- Platform (config/registry/gateway): https://github.com/Seenathul-Ilma/librasys-platform
- Frontend: https://github.com/Seenathul-Ilma/librasys-frontend