# <div align="center">  System Documentation: Bazaar Project </div>
---
<div align="center">
  
*A comprehensive guide to the system architecture, deployment, and operational aspects of the Bazaar Project.*

</div>

## 1. Target Environment

*   **Operating System(s) (e.g., Windows, Linux, macOS):**
    > For client mobile applications (Buyer and Seller), the target operating systems are **Android** and **iOS**. The Admin control panel is a web application accessed internally via standard web browsers on desktop operating systems (Windows, macOS, Linux). The backend service is designed to run in a Linux environment (within Docker containers on AWS EC2).

*   **Hardware Requirements (CPU, RAM, storage, GPU, etc.):**
    > Mobile applications are intended for standard smartphones, thus hardware requirements are minimal and consistent with the capabilities of modern Android and iOS devices. The web-based admin control panel has no specific hardware requirements beyond those needed to run a modern web browser. For the backend server (AWS EC2), initial requirements are modest, with the ability to scale as needed. S3 is used for image storage, reducing the storage load on the EC2 instance. A stable internet connection is necessary for all system components.

*   **Cloud vs. On-premise (e.g., AWS, Azure, Local server):**
    > The **AWS Cloud** platform is utilized. Backend logic (application business logic) is deployed on an **AWS EC2** instance, while **AWS S3** is used for storing static files (product images, etc.). The database (PostgreSQL) runs within a Docker container on the same EC2 instance.

## 2. Software Dependencies

*   **Required Runtimes (e.g., Java JDK, .NET, Node.js):**
    > The backend requires the **.NET 8.0 SDK/Runtime**. For frontend mobile applications (React Native) using Expo and EAS Build, **Node.js** (recommended LTS version specified by Expo) and **Java JDK** for Android builds are needed. The web admin panel (React) also requires **Node.js**.

*   **Libraries, Packages, or Frameworks (e.g., TensorFlow, Flask, React):**
    >
    > *   **Backend:** ASP.NET Core 8 (.NET 8), Entity Framework Core 8 (for interaction with PostgreSQL database), ASP.NET Core Identity (for authentication and authorization), Npgsql (PostgreSQL driver), JWT Bearer (for token authentication), SignalR (for real-time chat), AWSSDK.S3 (for S3 storage), FirebaseAdmin (for FCM push notifications).
    > *   **Frontend (Mobile Applications - Buyer & Seller):** React Native, Expo framework, Expo Router (for navigation), `@microsoft/signalr` (for chat), `expo-notifications` (for push notifications), `expo-secure-store` (for secure token storage), `@react-native-google-signin/google-signin`, `react-native-fbsdk-next` (for social login).
    > *   **Frontend (Admin Control Panel):** React, Vite (as a build tool).

*   **Containerization (e.g., Docker image and version):**
    > The database (PostgreSQL) runs as a Docker container, defined via a `docker-compose.yml` file. A custom image `lilhast1/bazaarbaza:v1.0` is used for PostgreSQL.

## 3. Installation and Configuration

*   **Software Installation Method (e.g., executable, script, Docker container):**
    >
    > Installation and system startup are detailed in the `README.md` files for the backend and frontend repositories.
    > *   **Backend:** Requires .NET 8 SDK and Docker. After cloning the repository, `docker-compose up` starts the PostgreSQL database. Then, `dotnet restore` installs dependencies, and `dotnet ef database update -c <DbContextName>` applies migrations for each DbContext. The project is run with `dotnet run`.
    > *   **Frontend (Mobile Applications):** Requires Node.js and npm/yarn. After cloning the repository and navigating to the specific application folder (Seller or Buyer), `npm install` (or `yarn install`) installs dependencies. The project is run locally with `npx expo start`. EAS Build is used for device builds.
    > *   **Frontend (Admin Control Panel):** Requires Node.js and npm/yarn. After cloning and navigating to the folder, `npm install` installs dependencies. The project is run locally with `npm run dev`.

*   **Required Environment Variables or Configuration Files:**
    >
    > The main configuration for the backend is located in `appsettings.json` (and `appsettings.Development.json`, `appsettings.Production.json`). Direct system environment variables are not used for core application logic; instead, values are read from these files.
    > *   **Key sections in `appsettings.json`:** `ConnectionStrings` (for all databases), `JwtSettings` (SecretKey, Issuer, Audience, ExpiryMinutes), `AWS` (Region), `S3Settings` (BucketName), `Firebase` (AdminSdkCredentialsPath - path to the JSON file with Firebase Admin SDK key), `MailSettings` (for sending emails), `AppSettings` (e.g., `AdminEmail` for ticketing).
    > *   **Sensitive data:** Specific API keys (e.g., for Google/Facebook OAuth, Firebase Admin SDK JSON file) are stored locally (e.g., in a `Secrets` folder which is in `.gitignore`) or managed via GitHub Secrets for CI/CD and environment variables on the deployment server (AWS). For production deployment on AWS, `appsettings.Production.json` is created on the server and contains production values for connection strings and other sensitive settings.

*   **System Initialization Steps (e.g., database setup, API keys):**
    >
    > 1.  Install Docker.
    > 2.  Start the PostgreSQL database using `docker-compose up -d` from the backend repository.
    > 3.  Restore backend dependencies with `dotnet restore`.
    > 4.  Apply all EF Core migrations sequentially for each DbContext: `dotnet ef database update -c <DbContextName>`.
    > 5.  Configure Firebase project: Create a project, add Android and iOS applications (with correct package name/bundle ID). Download `google-services.json` (for Android) and `GoogleService-Info.plist` (for iOS) and place them in the appropriate frontend mobile application folders. Generate a Firebase Admin SDK private key JSON file and store it in a secure location accessible to the backend (e.g., `Secrets` folder), then update the path in `appsettings.json` (`Firebase:AdminSdkCredentialsPath`).
    > 6.  Configure OAuth clients (Google, Facebook) and enter Client IDs in `appsettings.json` (`GoogleAuth` section).
    > 7.  Configure AWS S3 bucket and AWS credentials/region in `appsettings.Production.json` for deployment.
    > 8.  (Optional) Seed initial data (e.g., roles, admin account, geographical data) using the provided seeders in the backend application.

## 4. Continuous Integration / Continuous Delivery (CI/CD)

*   **Are CI/CD tools used? (e.g., GitHub Actions, Jenkins):**
    > Yes, **GitHub Actions** are used.

*   **What triggers deployment? (e.g., commit to main branch):**
    >
    > *   **Backend:** A GitHub Actions workflow (`.NET CI - Develop Branch`) is triggered on `push` to the `develop` branch and on `pull_request` targeting the `develop` branch. This workflow performs builds and tests. Actual deployment to AWS EC2 is currently done **manually** after a successful CI process.
    > *   **Frontend (Mobile Applications):** Deployment (creating builds) is done via the **EAS Build** service, which can be run manually (`eas build ...`) or integrated with GitHub Actions to trigger after a merge to a specific branch.
    > *   **Frontend (Admin Control Panel):** Deployed to **Netlify**. Deployment is triggered automatically via GitHub Actions integration with Netlify upon a `push` or `pull_request` to the `develop` branch (or `main` for production).

## 5. Network and Security

*   **Ports that need to be open on each system:**
    >
    > *   **Backend (AWS EC2 / Local):**
    >     *   Port for HTTP (e.g., **5054** locally, **80** on EC2 behind a load balancer/reverse proxy).
    >     *   Port for HTTPS (e.g., **7176** locally, **443** on EC2).
    >     *   Port for PostgreSQL database (**5432**) needs to be accessible to the backend application (locally or within the Docker network on EC2). It does not necessarily need to be open to the public internet.
    >     *   SignalR will use the same ports as HTTP/HTTPS for the initial connection, then attempt to establish a WebSocket connection.
    > *   **Frontend (Mobile Applications):** Use standard HTTP/HTTPS ports (80/443) for communication with the backend API. For the Expo Metro bundler during development, a port like `8081` (or random) is used.
    > *   **Frontend (Admin Control Panel):** Runs locally on a port like `5173` (Vite default). When deployed on Netlify, it uses standard HTTPS ports.

*   **SSL/TLS Requirements:**
    >
    > For the production environment, **SSL/TLS is mandatory** for all communication between client applications and the backend to encrypt data in transit.
    > *   **Local Development (Backend):** .NET development certificates are used (`dotnet dev-certs https --trust`).
    > *   **AWS EC2 (Backend):** DuckDNS is used for routing the IP address (non-elastic) to HTTPS. SSL termination is handled directly on the EC2 instance via a reverse proxy (Caddy).
    > *   **Netlify (Admin Panel):** Netlify automatically provides SSL/TLS for deployed sites.
    > *   **EAS Build/Expo (Mobile Applications):** Communication with the backend must go through an HTTPS URL.

*   **Authentication Mechanisms (e.g., OAuth, JWT):**
    >
    > **JWT (JSON Web Tokens)** are used for API request authentication. After a successful login (standard email/password, Google OAuth 2.0, or Facebook OAuth 2.0), the backend generates a JWT, which client applications store and send in the `Authorization: Bearer <token>` header of every protected API call.
    > ASP.NET Core Identity is used for managing users, passwords, and roles on the backend.

## 6. Database Deployment

*   **Required DBMS (e.g., PostgreSQL, MongoDB):**
    > **PostgreSQL** is used. It runs via a Docker image.

*   **Initialization Scripts or Migrations:**
    >
    > **EF Core Migrations** are used for initializing and updating the database schema. For each DbContext (`UsersDbContext`, `StoreDbContext`, `CatalogDbContext`, `OrdersDbContext`, `NotificationsDbContext`, `InventoryDbContext`, `ReviewDbContext`, `AdDbContext`, `ConversationDbContext`), there is a set of migrations. Migrations are applied with the command `dotnet ef database update -c <DbContextName>` after `dotnet restore` and starting the Docker container for the database.
    > The `dotnet-ef` tool must be installed (`dotnet tool install --global dotnet-ef`).

## 7. Rollback and Recovery

*   **How can a deployment be rolled back to a previous state?**
    >
    > *   **Backend (AWS EC2):** Rollback can be done by checking out a previous stable commit or tag from the Git repository, rebuilding (`dotnet publish`), and restarting the service (`sudo systemctl restart bazaar-backend.service`).
    > *   **Database:** EF Core migrations support reverting to a previous migration (`dotnet ef database update <PreviousMigrationName> -c <DbContextName>`).
    > *   **Frontend (Netlify):** Netlify keeps a history of deployments and allows easy rollback to a previously successfully deployed version via their dashboard.
    > *   **Frontend (Mobile Applications - EAS):** EAS Build also keeps a history of builds. If a new build has issues, a previous stable build can be distributed (if the `.apk`/`.aab`/`.ipa` file is saved).

*   **Backup Procedures (especially for databases):**
    >
    > **PostgreSQL in Docker on EC2:** Backup is **critical** and must be configured manually or via scripts. Standard PostgreSQL tools like `pg_dump` can be used to create logical backups. These backup files should then be regularly copied to a secure location.

## 8. Monitoring and Logging

*   **Tools Used (e.g., Prometheus, ELK Stack):**
    >
    > Specific advanced monitoring tools like Prometheus or ELK Stack are not used. Basic monitoring utilizes:
    > *   **Firebase Console:** Provides charts for tracking FCM usage.
    > *   **AWS Console:** Provides metrics for the EC2 instance (CPU, network, disk). AWS CloudWatch for logs from EC2.
    > *   **Application Logging (.NET):** Logs are generated using the standard `Microsoft.Extensions.Logging` and are output to the terminal (locally) or system logs on the server (e.g., `journalctl` on Linux for the EC2 service).

*   **Generated Logs and Their Location:**
    >
    > *   **Local Development:** Logs are output directly to the terminal where `dotnet run` is executed.
    > *   **AWS EC2 (Production):** Logs are captured by `journald` and can be viewed using `sudo journalctl -u bazaar-backend.service -f --no-pager`. It's possible to specify the amount of logs to display (e.g., the last 100 lines).
    > *   Logging levels (`Information`, `Warning`, `Error`) are configured in `appsettings.json`.

## 9. User Access and Roles

*   **Who can access the deployed system?**
    >
    > *   **Buyers:** Can register and use their mobile application.
    > *   **Sellers:** Can register and use their mobile application. Seller registration requires Admin approval.
    > *   **Admin:** Predefined account (one consistent account). Accesses the web admin control panel.
    >
    > All three roles (Admin, Buyer, Seller) are defined in the system. Buyers and Sellers can also log in via their Facebook and Google accounts.

*   **How are users assigned and managed?**
    >
    > The Admin has full visibility into all users. They can activate/deactivate them and approve registrations (especially for Sellers). The Admin is also responsible for technical support via a ticketing system. The Admin panel provides analytics on stores, sales, etc.

## 10. Testing in Deployment Environment

*   **Smoke tests or sanity check procedures post-deployment:**
    >
    > Testing is performed both locally and on the deployed system.
    > *   **Backend:** Swagger UI is used for testing each API endpoint. Unit and Integration tests are written (`dotnet test`).
    > *   **Frontend (Mobile Applications):** Expo and EAS Build are used for quickly creating builds that are installed on physical devices for testing functionality in conjunction with the backend (first local, then deployed).
    >
    > There is no formalized set of smoke tests that automatically run post-deployment; reliance is placed on manual testing of key functionalities via Swagger and client applications.

## 11. Step-by-Step Deployment to a Clean Environment

*   **Prepare a step-by-step guide for environment and system setup.**
    >
    > 1.  **Server Preparation (AWS EC2 - e.g., Ubuntu Linux):**
    >     *   Create an EC2 instance.
    >     *   Configure Security Groups (open ports 80, 443, 22 for SSH).
    >     *   Install Docker and Docker Compose.
    >     *   Install .NET 8 SDK/Runtime.
    >     *   (Optional) Install and configure Caddy (or another reverse proxy) for HTTPS and routing.
    > 2.  **Database Setup:**
    >     *   Copy `docker-compose.yml` to the server.
    >     *   Start the PostgreSQL container: `sudo docker-compose up -d`.
    >     *   Verify the database is running: `sudo docker ps`.
    > 3.  **Backend Deployment:**
    >     *   Clone the backend repository: `git clone <repo_url> bazaar-backend`.
    >     *   Navigate to the `bazaar-backend` folder.
    >     *   Checkout the desired branch (e.g., `develop` or `main/master`).
    >     *   Create `appsettings.Production.json` with production connection strings (targeting Docker PostgreSQL at `localhost:5432` within EC2), JWT settings, AWS S3 settings, Firebase key path (which also needs to be securely transferred to the server), email settings, and Admin email. **Ensure permissions on this file are restrictive.**
    >     *   Transfer the Firebase Admin SDK JSON key to the server at the location specified in `appsettings.Production.json`.
    >     *   Run `dotnet restore`.
    >     *   Apply ALL EF Core migrations for ALL DbContexts sequentially: `dotnet ef database update -c <DbContextName>`. Carefully monitor the output of each command.
    >     *   Publish the application: `dotnet publish --configuration Release -o ./publish`.
    >     *   Copy `appsettings.Production.json` to the `./publish` folder.
    >     *   Configure and run the backend application as a systemd service to run in the background and start automatically.
    > 4.  **Frontend Admin Panel Deployment (Netlify):**
    >     *   Connect the Admin Panel's GitHub repository to your Netlify account.
    >     *   Configure build commands (e.g., `npm run build`) and the publish directory (e.g., `dist`).
    >     *   Set environment variables on Netlify if needed (e.g., backend API URL).
    >     *   Deployment is triggered automatically on push to the appropriate branch.
    > 5.  **Frontend Mobile Applications Deployment (EAS Build & Submit):**
    >     *   Ensure `google-services.json` and `GoogleService-Info.plist` are in the repository.
    >     *   Configure `app.json` with the correct `package`/`bundleIdentifier` and `plugins` (especially `expo-notifications` and EAS `projectId`).
    >     *   Upload necessary credentials (FCM V1 key, APNs .p8 key) to EAS: `eas credentials`.
    >     *   Run production builds: `eas build -p android --profile production` and `eas build -p ios --profile production`.
    >     *   After successful builds, download the `.apk`/`.aab` and `.ipa` files.
    >     * (Additional)  Upload to Google Play Store and Apple App Store Connect using `eas submit -p android` and `eas submit -p ios`, or manually.
    > 6.  **Final Testing:** Verify all functionalities on the deployed system.
