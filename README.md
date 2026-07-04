# Student Registration System

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

## Jenkins CI/CD Pipeline

This project uses a **Jenkins Declarative Pipeline** to automate build, test, and deployment of the Flask application.

### Pipeline Flow

GitHub Push
↓
Jenkins Webhook Trigger
↓
Checkout Source Code
↓
Install Dependencies (pip)
↓
Run Unit Tests (pytest)
↓
Build Docker Container
↓
Deploy Flask Application
↓
Send Email Notification (Success / Failure)

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
- Email sent on both:
  - ✅ Success
  - ❌ Failure
    
<img width="312" height="211" alt="image" src="https://github.com/user-attachments/assets/b088db36-b6f6-4dbd-843e-07998fa489ef" />


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

## Key Learnings

- CI/CD automation using Jenkins
- GitHub webhook integration
- Docker-based deployment
- Secure credential management in Jenkins
- Multi-node Jenkins execution handling
- Automated testing using pytest

---

## License

MIT License

---



