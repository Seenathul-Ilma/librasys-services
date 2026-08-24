# LibraSys — Microservices Services Repository (Super Repository)

**Student Name:** Seenathul Ilma  
**Student Number:** 241711005  
**Slack Handle:** Seenathul Ilma Musawwir  
**GCP Project ID:** librasys-eca 

---

## 📖 Project Description
This super repository contains all backend business microservices for the **LibraSys Library Management System**, organized using **Git Submodules** in a polyrepo architecture.

### Submodule Business Services:
1. **[User Service](file:///c:/IJSE/Enterprice%20Cloud%20Architecture/ECA-CW-LIBRARY-MANAGEMENT-SYSTEM/librasys-services/user-service)** (`user-service`): Manages user accounts (MySQL / Cloud SQL).
2. **[Book Service](file:///c:/IJSE/Enterprice%20Cloud%20Architecture/ECA-CW-LIBRARY-MANAGEMENT-SYSTEM/librasys-services/book-service)** (`book-service`): Manages book catalogue (MongoDB) & cover uploads (Google Cloud Storage).
3. **[Loan Service](file:///c:/IJSE/Enterprice%20Cloud%20Architecture/ECA-CW-LIBRARY-MANAGEMENT-SYSTEM/librasys-services/loan-service)** (`loan-service`): Manages borrowing & returning of books (MySQL / Cloud SQL).

---

## 🛠️ Technology Stack
- **Java 25**
- **Spring Boot 4.1.1**
- **Spring Data JPA & Spring Data MongoDB**
- **Spring Cloud Eureka Client & Config Client**
- **Google Cloud Storage SDK**

---

## 🚀 Setup & Initialization with Submodules

```bash
# Clone parent repository with submodules
git clone --recursive <repository-url>

# Or update submodules if already cloned
git submodule update --init --recursive
```
