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

-----

## 📌 About

AnimalCare is a full-stack web application that manages the daily operations of a veterinary clinic. Owners register their animals, receptionists book appointments, veterinarians complete consultations that **automatically generate medical history records via SQL triggers**, and the system pushes **real-time notifications** on every appointment event.

Access control is handled by a custom `AuthorizeRoleAttribute` session filter — **no ASP.NET Identity** — enforcing 3 permission levels across 10 controllers.

The project ships with **two complete implementations**: a **DB-First** and a **Code-First** EF Core workflow.

> Built for **420-DW4-AS** — Web Server Application Development @ LaSalle College.

-----

## ⚙️ Tech Stack

|Layer        |Technology                                                     |
|:------------|:--------------------------------------------------------------|
|**Framework**|ASP.NET Core MVC 8                                             |
|**ORM**      |Entity Framework Core (DB-First + Code-First)                  |
|**Database** |SQL Server (9 tables, 4 views, 6 stored procedures, 4 triggers)|
|**Language** |C#                                                             |
|**Frontend** |Razor Views, Bootstrap                                         |
|**Auth**     |Custom session-based `AuthorizeRoleAttribute` filter           |

-----

## 🗄️ Database

### Tables

|Table         |Description                                                          |
|:-------------|:--------------------------------------------------------------------|
|`Role`        |3 roles — Administrateur, Réceptionniste, Vétérinaire                |
|`Utilisateur` |Users with credentials and role FK                                   |
|`Veterinaire` |Vet profiles (specialty, contact), linked to Utilisateur             |
|`Proprietaire`|Pet owners (name, phone, email, address)                             |
|`Animal`      |Pets (species, breed, sex, age, weight), linked to Proprietaire      |
|`Horaire`     |Vet weekly schedules (Disponible / Occupé / Congé)                   |
|`RendezVous`  |Appointments (vet + animal + datetime + status + motif)              |
|`Historique`  |Medical records (diagnosis + treatment), auto-generated on completion|
|`Notification`|System alerts tied to appointment lifecycle events                   |

### Entity Relationship Diagram

```
Role ──1:N──> Utilisateur ──1:N──> Veterinaire ──1:N──> Horaire
                                       │
                                       ├──1:N──> RendezVous ──1:N──> Notification
                                       │              │
                                       │              │
                                       ├──1:N──> Historique
                                       │              │
Proprietaire ──1:N──> Animal ──────────┘──────────────┘
```

### Views

|View                    |Joins                                            |
|:-----------------------|:------------------------------------------------|
|`V_RendezVousDetails`   |RendezVous + Veterinaire + Animal                |
|`V_AnimauxProprietaires`|Animal + Proprietaire                            |
|`V_HistoriqueComplet`   |Historique + Animal + Veterinaire                |
|`V_HorairesDisponibles` |Horaire + Veterinaire (where status = Disponible)|

### Stored Procedures

|Procedure                 |What it does                                            |
|:-------------------------|:-------------------------------------------------------|
|`AjouterRendezVous`       |Books a new appointment (status = Planifié)             |
|`ModifierStatutRendezVous`|Updates appointment status (Planifié → Terminé / Annulé)|
|`SupprimerRendezVous`     |Deletes an appointment by ID                            |
|`AjouterHistorique`       |Creates a medical history entry                         |
|`AjouterProprietaire`     |Registers a new pet owner                               |
|`MarquerNotificationLue`  |Flags a notification as read                            |

### Triggers

|Trigger                 |Fires on                         |Result                              |
|:-----------------------|:--------------------------------|:-----------------------------------|
|`TRG_RendezVous_Ajout`  |`AFTER INSERT` on RendezVous     |Creates a “Création” notification   |
|`TRG_RendezVous_Annule` |`AFTER UPDATE` (status → Annulé) |Creates an “Annulation” notification|
|`TRG_RendezVous_Termine`|`AFTER UPDATE` (status → Terminé)|**Auto-inserts** a Historique record|
|`TRG_Horaire_Modif`     |`AFTER UPDATE` on Horaire        |Notifies when a vet schedule changes|

-----

## 🔐 Role-Based Access Control

The custom `AuthorizeRoleAttribute` reads `UserId` and `RoleId` from the session on every request. Unauthorized users are redirected to login; wrong roles get an Access Denied page.

|Controller   |Admin|Réceptionniste|Vétérinaire|
|:------------|:---:|:------------:|:---------:|
|Home         |✅    |✅             |✅          |
|RendezVous   |✅    |✅             |✅          |
|Historiques  |✅    |✅             |✅          |
|Notifications|✅    |✅             |✅          |
|Animals      |✅    |✅             |❌          |
|Proprietaires|✅    |✅             |❌          |
|Horaires     |✅    |❌             |✅          |
|Veterinaires |✅    |❌             |❌          |
|Utilisateurs |✅    |❌             |❌          |
|Roles        |✅    |❌             |❌          |

-----

## 🏗️ Project Structure

```
AnimalCare/
│
├── AnimalCareMVC_DBFirst/              # DB-First implementation
│   ├── Controllers/
│   │   ├── AuthController.cs           # Login / Logout / AccessDenied
│   │   ├── HomeController.cs           # Dashboard
│   │   ├── AnimalsController.cs        # CRUD + Rapport
│   │   ├── RendezVousController.cs     # CRUD + Rapport (uses stored procedures)
│   │   ├── HistoriquesController.cs    # CRUD
│   │   ├── HorairesController.cs       # CRUD
│   │   ├── NotificationsController.cs  # CRUD + mark as read
│   │   ├── ProprietairesController.cs  # CRUD
│   │   ├── VeterinairesController.cs   # CRUD
│   │   ├── UtilisateursController.cs   # CRUD
│   │   └── RolesController.cs          # CRUD
│   ├── Filters/
│   │   └── AuthorizeRoleAttribute.cs   # Custom session-based auth
│   ├── Models/
│   │   ├── AnimalCareContext.cs         # DbContext — 9 entities + 4 views
│   │   ├── Animal.cs
│   │   ├── Veterinaire.cs
│   │   ├── Proprietaire.cs
│   │   ├── RendezVou.cs
│   │   ├── Historique.cs
│   │   ├── Horaire.cs
│   │   ├── Notification.cs
│   │   ├── Utilisateur.cs
│   │   ├── Role.cs
│   │   └── V*.cs                       # View models (4 SQL views)
│   └── Views/                          # Razor views per entity
│
├── AnimalCareMVC_CodeFirst/            # Code-First implementation
│   ├── Controllers/                    # Same structure as DB-First
│   ├── Filters/
│   ├── Models/
│   ├── Migrations/                     # EF Core migration files
│   └── Views/
│
├── Database_schema/
│   └── CliniqueVetScript.sql           # Complete SQL — tables, seed data, views, SPs, triggers
│
└── Documentation/
    ├── CliniqueVeterinaire.pdf
    ├── ReadMeCodeFirst.txt
    └── readme_DBFirst.txt
```

-----

## 🚀 Getting Started

### Prerequisites

- [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- SQL Server (LocalDB or SQLEXPRESS)
- Visual Studio 2022

### Option 1 — DB-First

1. **Clone**
   
   ```bash
   git clone https://github.com/safwan-islam/Animal-Care.git
   ```
1. **Create the database** — open `Database_schema/CliniqueVetScript.sql` in SSMS and execute. This creates the database, all 9 tables, seed data, 4 views, 6 stored procedures, and 4 triggers.
1. **Open** `AnimalCareMVC_DBFirst.sln` in Visual Studio
1. **Update** the connection string in `appsettings.json`:
   
   ```json
   "ConnectionStrings": {
     "DefaultConnection": "Server=localhost\\SQLEXPRESS;Database=CliniqueVeterinaireDB;Trusted_Connection=True;Encrypt=False;"
   }
   ```
1. **Run** the project

### Option 2 — Code-First

1. **Open** `AnimalCareMVC_CodeFirst.sln` in Visual Studio
1. **Update** the connection string in `appsettings.json`
1. **Apply migrations**:
   
   ```bash
   dotnet ef database update
   ```
1. **Run** the project

### Default Credentials

|Username   |Password  |Role          |
|:----------|:---------|:-------------|
|`admin`    |`admin123`|Administrateur|
|`reception`|`recep123`|Réceptionniste|
|`vet1`     |`vet123`  |Vétérinaire   |
|`vet2`     |`vet456`  |Vétérinaire   |

-----

## 👥 Authors

- **Safwan-Ahmed Islam** — [GitHub](https://github.com/safwan-islam) · [LinkedIn](https://www.linkedin.com/in/safwan-ahmed-islam)
