<h1 align="center">🐾 AnimalCare</h1>

<p align="center">
  <strong>Veterinary Clinic Management System</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/.NET-8.0-512BD4?style=flat-square&logo=dotnet&logoColor=white" />
  <img src="https://img.shields.io/badge/C%23-12-239120?style=flat-square&logo=csharp&logoColor=white" />
  <img src="https://img.shields.io/badge/SQL_Server-CC2927?style=flat-square&logo=microsoftsqlserver&logoColor=white" />
  <img src="https://img.shields.io/badge/EF_Core-512BD4?style=flat-square&logo=dotnet&logoColor=white" />
  <img src="https://img.shields.io/badge/Bootstrap-7952B3?style=flat-square&logo=bootstrap&logoColor=white" />
</p>

---

## 📌 About

AnimalCare is a full-stack web application that manages the daily operations of a veterinary clinic. Owners register their animals, receptionists book appointments, veterinarians complete consultations that **automatically generate medical history records via SQL triggers**, and the system pushes **real-time notifications** on every appointment event.

Access control is handled by a custom `AuthorizeRoleAttribute` session filter — **no ASP.NET Identity** — enforcing 3 permission levels across 10 controllers.

The project ships with **two complete implementations**: a **DB-First** and a **Code-First** EF Core workflow.

> Built for **420-DW4-AS** — Web Server Application Development @ LaSalle College.

---

## ⚙️ Tech Stack

| Layer | Technology |
|:------|:-----------|
| **Framework** | ASP.NET Core MVC 8 |
| **ORM** | Entity Framework Core (DB-First + Code-First) |
| **Database** | SQL Server (9 tables, 4 views, 6 stored procedures, 4 triggers) |
| **Language** | C# |
| **Frontend** | Razor Views, Bootstrap |
| **Auth** | Custom session-based `AuthorizeRoleAttribute` filter |

---

## 🗄️ Database

### Tables

| Table | Description |
|:------|:------------|
| `Role` | 3 roles — Administrateur, Réceptionniste, Vétérinaire |
| `Utilisateur` | Users with credentials and role FK |
| `Veterinaire` | Vet profiles (specialty, contact), linked to Utilisateur |
| `Proprietaire` | Pet owners (name, phone, email, address) |
| `Animal` | Pets (species, breed, sex, age, weight), linked to Proprietaire |
| `Horaire` | Vet weekly schedules (Disponible / Occupé / Congé) |
| `RendezVous` | Appointments (vet + animal + datetime + status + motif) |
| `Historique` | Medical records (diagnosis + treatment), auto-generated on completion |
| `Notification` | System alerts tied to appointment lifecycle events |

### Entity Relationship Diagram

