# Student Registration System | CI/CD with Jenkins & GitHub Actions

A simple **Flask** web application to manage student records with **MongoDB** as the backend database. Users can **add, view, update, and delete** student details.

---

## Features

* List all students on the home page
* Add a new student
* Update existing student details
* Delete a student with confirmation
* Simple and responsive UI using Bootstrap

---

## Tech Stack

* **Backend:** Python, Flask
* **Database:** MongoDB (via Flask-PyMongo)
* **Frontend:** HTML, Jinja2 templates, Bootstrap 5
* **Environment Variables:** Managed via `.env` file

---

## Setup Instructions

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd <repo-folder>
```

### 2. Create and activate a virtual environment

```bash
python -m venv venv
# Activate venv
# Windows:
venv\Scripts\activate
# Linux / Mac:
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

**`requirements.txt` example:**

```
Flask
Flask-PyMongo
python-dotenv
bson
```

### 4. Configure environment variables

Create a `.env` file in the project root:

```
MONGO_URI=<your-mongodb-connection-string>
SECRET_KEY=<your-secret-key>
```

### 5. Run the application

```bash
python app.py
```

Open your browser at: [http://localhost:8000](http://localhost:8000)

---

## Project Structure

```
project/
│
├── templates/
│   ├── base.html
│   ├── index.html
│   ├── add_student.html
│   ├── update_student.html
│
├── app.py
├── requirements.txt
└── .env
```

---

## Screenshots

**Home Page**
Lists all students with Edit/Delete buttons.
- <img width="1902" height="607" alt="image" src="https://github.com/user-attachments/assets/a58a6a6d-4978-4769-8074-232e4d31e69d" />


**Add Student**
Form to add a new student.
- <img width="1897" height="801" alt="image" src="https://github.com/user-attachments/assets/d65d25c3-ebb5-410a-adb1-e130ad7c5878" />


**Update Student**
Form pre-filled with student details.
- <img width="1905" height="897" alt="image" src="https://github.com/user-attachments/assets/04febf01-879f-431f-ab07-abcfb993acf1" />

---

## Notes

* Make sure MongoDB is running and accessible via the URI in `.env`
* Delete action includes a confirmation page to prevent accidental deletion
* Uses `ObjectId` from `bson` to work with MongoDB document IDs
* If you use MongoDB Atlas on macOS, install dependencies again (`pip install -r requirements.txt`). This project now uses `certifi` CA bundle explicitly to avoid common TLS certificate verification failures with `pymongo`.

---

# CI/CD Pipelines

```text
                    Developer
                        │
                 Push Source Code
                        │
                GitHub Repository
                        │
        ┌───────────────┴───────────────┐
        │                               │
        ▼                               ▼
   Jenkins Pipeline             GitHub Actions
        │                               │
   Checkout Code                 Checkout Code
        │                               │
 Install Dependencies      Setup Python + Install Dependencies
        │                               │
     Run Tests                    Run Tests (pytest)
        │                               │
   Build Docker Image          Build Docker Image
        │                               │
 Deploy Flask Container     Deploy to Staging (staging branch)
        │                               │
        └───────────────┬───────────────┘
                        │
               Production Deployment
                  (Git Tag Release)
                        │
                        ▼
                  Flask Application
                        │
                        ▼
                  MongoDB Atlas
```                  

## Jenkins CI/CD Pipeline

This project uses a **Jenkins Declarative Pipeline** to automate build, test, and deployment of the Flask application.

### Architecture Diagram

<img width="1014" height="677" alt="image" src="https://github.com/user-attachments/assets/aa21bab1-37a5-400c-86c2-00308c6defb9" />

### Pipeline Flow

<img width="1513" height="416" alt="image" src="https://github.com/user-attachments/assets/26568e6e-4192-491b-8b5f-71152b2a4025" />

---

## Jenkins Pipeline Stages

### 1. Checkout
- Pulls latest code from GitHub repository

### 2. Build
- Installs all dependencies from `requirements.txt`

### 3. Test
- Runs unit tests using `pytest`
- Pipeline fails if any test fails

<img width="701" height="228" alt="image" src="https://github.com/user-attachments/assets/d1d60736-9d6c-4061-9f1f-0c2927dc36aa" />

### 4. Deploy
- Runs Flask application inside a Docker container
- Exposes application on port `8000`

### 5. Verify Deployment
- Runs docker ps command
- Show container list

<img width="1024" height="99" alt="image" src="https://github.com/user-attachments/assets/11cf58b1-b540-4c59-984e-a0e2f31d45ea" />

### 6. Notification
- Sends email using Jenkins **Extended Email Notification plugin**

<img width="312" height="211" alt="image" src="https://github.com/user-attachments/assets/b088db36-b6f6-4dbd-843e-07998fa489ef" />

- Email sent on both:
  - ✅ Success
  - ❌ Failure
    
<img width="503" height="418" alt="image" src="https://github.com/user-attachments/assets/7bb6d7e3-5188-4848-896f-431b93425928" />

<img width="543" height="557" alt="image" src="https://github.com/user-attachments/assets/51f1d81a-58e5-439e-8870-96f413878cc0" />

---

## Jenkins Webhook Integration

- GitHub webhook triggers Jenkins automatically on every push to `main` branch
- No manual build required
- Jenkins listens at: /github-webhook/

---

## Docker Deployment

The application is deployed using Docker:

- Base Image: `python:3.12`
- Flask runs inside container
- Port mapping: `8000:8000`

---

## Environment Variables

The application uses Jenkins credentials securely:

- `MONGO_URI`
- `SECRET_KEY`

These are injected using Jenkins **Credentials Binding Plugin**.

---

## GitHub Actions CI/CD Pipeline

This project also uses **GitHub Actions** to automate the Continuous Integration and Continuous Deployment (CI/CD) process for the Flask application.

### Workflow Architecture

```text
                 GitHub Repository
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
 Push to main    Push to staging    Push Tag (v1.0.0)
        │               │                │
        ▼               ▼                ▼
  Install Deps    Install Deps     Install Deps
        ▼               ▼                ▼
    Run Tests      Run Tests       Run Tests
        ▼               ▼                ▼
 Build Docker    Build Docker    Build Docker
        │               │                │
        │        Deploy to Staging       │
        │               │                │
        └───────────────┼────────────────┘
                        ▼
             Deploy to Production
```

### GitHub Secrets

Sensitive information is securely stored using **GitHub Repository Secrets**.

The following secrets are configured:

<img width="2596" height="506" alt="image" src="https://github.com/user-attachments/assets/ad16bbf3-6231-473e-83f3-a75fe6f4a4f5" />

These secrets are injected into the workflow during execution and are never stored in the source code.

### GitHub Actions Pipeline

<img width="3382" height="1734" alt="image" src="https://github.com/user-attachments/assets/124ef121-acb8-4934-a148-ebf99cf2bf63" />

---

## Deployment Strategy

### Staging Deployment

- Triggered automatically on every push to the **staging** branch.
- Builds the Docker image.
- Starts the application container for deployment verification.

### Production Deployment

- Triggered automatically when a Git tag beginning with **v** (for example `v1.0.0`) is pushed.
- Builds the Docker image.
- Deploys the application as the production release.

---

## Key Learnings

- CI/CD automation using Jenkins
- GitHub webhook integration
- Docker-based deployment
- Secure credential management in Jenkins
- Multi-node Jenkins execution handling
- Automated testing using pytest

- GitHub Actions CI/CD
- Automated Python dependency installation
- Automated testing using pytest
- Docker image creation
- Branch-based deployment strategy
- Tag-based production deployment
- Secure secret management using GitHub Secrets
- Continuous Integration and Continuous Deployment (CI/CD)

---

## License

MIT License

---



