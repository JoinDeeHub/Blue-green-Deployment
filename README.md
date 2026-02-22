# Blue-Green Deployment using Docker & Kubernetes (Minikube)

## 📌 Overview

This project demonstrates containerization and blue-green deployment of a Node.js application using:

- Docker & Docker Compose\
- Kubernetes (Minikube)\
- MongoDB\
- Blue-Green Deployment Strategy

The application consists of:

- Backend (Express + MongoDB)\
- Frontend Blue version\
- Frontend Green version\
- Kubernetes-based traffic switching

---

## 🏗 Architecture Overview

### Local / Docker Architecture

User → Frontend (Blue/Green) → Backend → MongoDB

### Kubernetes Architecture

User\
↓\
NodePort Service (frontend-service)\
↓\
Selector-based routing (version=blue / version=green)\
↓\
Frontend Pods\
↓\
Backend Service\
↓\
MongoDB Service

<img width="499" height="523" alt="image" src="https://github.com/user-attachments/assets/c5709a6a-207b-4ae1-9f41-fdf184496f24" />


<img width="1359" height="516" alt="Screenshot from 2026-02-22 20-53-46" src="https://github.com/user-attachments/assets/b39508d9-3776-4a98-a0bb-a581cbd39f74" />

---

# 🚀 Part 1 -- Local Deployment

## 1️⃣ Clone Repository

```
git clone <repository-url>\
cd Blue-green-Deployment

2️⃣ Install Dependencies
------------------------

Backend:

cd backend\
npm install

Frontend Blue:

cd ../frontend-blue\
npm install

Frontend Green:

cd ../frontend-green\
npm install

3️⃣ MongoDB Setup
-----------------

Using Docker:

docker run -d -p 27017:27017 --name mongodb mongo

4️⃣ Start Services
------------------

Backend:

npm start

Frontend:

npm start

Verify:

-   Backend: <http://localhost:5000/health>

-   Frontend accessible

-   Registration successful

-   Data stored in MongoDB

* * * * *

```

🐳 Part 2 -- Containerization
============================

```

🔹 Backend Dockerfile
---------------------

-   Node 18 Alpine

-   Exposes port 5000

-   Uses environment variable for MongoDB connection

🔹 Frontend Dockerfiles
-----------------------

-   Node 18 Alpine

-   Blue runs on port 3100

-   Green runs on port 3200

🔹 docker-compose.yml
---------------------

Services:

-   mongodb

-   backend

-   frontend-blue

-   frontend-green

Run:

docker compose up --build

Verification:

docker ps

Application accessible via:

-   Blue → http://localhost:3002

-   Green → http://localhost:3001

-   Backend → <http://localhost:5000/health>

* * * * *
```

☸ Part 3 -- Kubernetes Deployment (Minikube)
===========================================
```
1️⃣ Start Minikube
------------------

minikube start

2️⃣ Build Images Inside Minikube
--------------------------------

eval $(minikube docker-env)

docker build -t backend-image ./backend\
docker build -t frontend-blue-image ./frontend-blue\
docker build -t frontend-green-image ./frontend-green

3️⃣ Kubernetes Manifests Created
--------------------------------

-   mongodb-deployment.yaml

-   mongodb-service.yaml

-   backend-deployment.yaml

-   backend-service.yaml

-   frontend-blue.yaml

-   frontend-green.yaml

-   frontend-service.yaml

Important:\
Custom images use:

imagePullPolicy: Never

4️⃣ Deploy to Cluster
---------------------

kubectl apply -f k8s/

Verify:

kubectl get pods\
kubectl get svc

Access Application:

minikube service frontend-service

Registration successful confirms:

-   Frontend → Backend

-   Backend → MongoDB

-   Kubernetes networking working

* * * * *
```

🔵🟢 Part 4 -- Blue-Green Deployment Implementation
==================================================
```
Two frontend deployments were created:

-   frontend-blue (version=blue)

-   frontend-green (version=green)

Both run simultaneously.

🔹 Service-Based Traffic Routing
--------------------------------

The frontend-service routes traffic based on labels:

selector:\
  app: frontend\
  version: blue

🔹 Switch to Green
------------------

kubectl patch service frontend-service \\
-p '{"spec":{"selector":{"app":"frontend","version":"green"}}}'

🔹 Switch Back to Blue
----------------------

kubectl patch service frontend-service \\
-p '{"spec":{"selector":{"app":"frontend","version":"blue"}}}'

Traffic switching occurs instantly without downtime because both versions are running simultaneously.

* * * * *

🧠 Blue-Green Deployment Strategy
=================================

This implementation uses:

-   Label-based routing

-   Separate deployments for each version

-   Kubernetes Service selector modification

-   Zero-downtime traffic switching

Both versions remain active during deployment, ensuring high availability.

```

<img width="1063" height="285" alt="image" src="https://github.com/user-attachments/assets/ed053b8d-37a7-41c5-8e97-bb3e071e24fd" />


* * * * *

⚠ Challenges Faced & Solutions
==============================

| Challenge | Solution |
| --- | --- |
| Docker port conflict | Changed host port mapping |
| MongoDB connection failure | Used service name instead of localhost |
| Kubernetes ErrImagePull | Added `imagePullPolicy: Never` |
| Service unreachable | Corrected targetPort based on container port |
| Blue-Green switch routing issue | Patched both selector and targetPort |

* * * * *

📸 Screenshots Included
=======================

-   Local deployment working
-   Docker containers running
-   Kubernetes pods & services running
-   Successful registration in cluster
-   Blue → Green switch demonstration
-   Green → Blue rollback demonstration

<img width="1360" height="760" alt="Screenshot from 2026-02-21 22-38-49" src="https://github.com/user-attachments/assets/aa3f36f5-e735-46ff-84de-2e65982ef665" />

<img width="1360" height="760" alt="Screenshot from 2026-02-21 22-39-01" src="https://github.com/user-attachments/assets/f07ce36a-bc78-4d94-bb79-89a12ce733a4" />

<img width="1360" height="760" alt="Screenshot from 2026-02-21 22-39-59" src="https://github.com/user-attachments/assets/63fd3ce0-4ca3-44aa-9347-c607bb25db77" />

<img width="1360" height="760" alt="Screenshot from 2026-02-21 22-40-56" src="https://github.com/user-attachments/assets/3eb427c8-deba-4072-b7fd-aa8e83f171bd" />

<img width="535" height="760" alt="Screenshot from 2026-02-21 22-54-01" src="https://github.com/user-attachments/assets/a62a9ef2-6b60-458b-b92a-97226d2c261d" />

<img width="1359" height="760" alt="Screenshot from 2026-02-21 23-28-47" src="https://github.com/user-attachments/assets/4c8bf8ca-db1f-4a6d-9c4a-d5248f9dfac8" />

<img width="1359" height="760" alt="Screenshot from 2026-02-22 00-00-24" src="https://github.com/user-attachments/assets/209272c5-3b7c-4b0a-a816-20a826986364" />

<img width="1359" height="516" alt="Screenshot from 2026-02-22 15-31-29" src="https://github.com/user-attachments/assets/35e6cfc0-4fcc-40af-bb88-dd20f12b63cc" />

<img width="1359" height="698" alt="Screenshot from 2026-02-22 11-30-32" src="https://github.com/user-attachments/assets/dc4920fd-5db7-4c51-9cae-67cddf904a00" />

<img width="1359" height="698" alt="Screenshot from 2026-02-22 11-30-25" src="https://github.com/user-attachments/assets/f8ddcda1-c2df-4d4b-a3b1-39be68f52959" />


* * * * *
```
🎯 Key Concepts Demonstrated
============================

-   Containerization using Docker

-   Multi-service orchestration

-   Kubernetes Deployments & Services

-   Readiness probes

-   NodePort exposure

-   Blue-Green deployment strategy

-   Zero-downtime traffic switching

-   Service selector-based routing

* * * * *
```

```
🏁 Conclusion
=============

This project successfully demonstrates a complete DevOps lifecycle:

-   Local setup

-   Containerization

-   Kubernetes deployment

-   Blue-Green production strategy

-   Zero-downtime traffic switching

The implementation showcases strong understanding of Docker networking, Kubernetes service routing, and real-world deployment strategies.
```
