Real-Time Weld Quality Inspection Using Computer Vision
Project Overview

Real-Time Weld Quality Inspection Using Computer Vision is an AI-based automated inspection system designed to identify weld defects from images and video.

The system uses YOLO-based object detection to detect weld defects and provides inspection results through a web-based dashboard. The complete workflow connects the AI model with a FastAPI backend, database, and React frontend.

System Workflow

Weld Image/Video → YOLO Model → FastAPI → Database → React Dashboard

The system identifies the following weld defect classes:

Crack
Porosity
Undercut

Defect-free weld images are recorded as PASS, while detected defects result in a FAIL inspection.

Objectives
Automate weld quality inspection using computer vision.
Detect common weld defects using a YOLO object-detection model.
Reduce dependency on manual visual inspection.
Provide inspection results through an interactive dashboard.
Store inspection metadata for later analysis.
Display defect location, confidence, inspection status, and processing time.
Technologies Used
AI and Computer Vision
Python
YOLO
Ultralytics
OpenCV
PyTorch
Backend
FastAPI
Uvicorn
SQLAlchemy
Pydantic
Frontend
React
Vite
JavaScript
Database
SQLite
MySQL

The project requirements include FastAPI, Uvicorn, SQLAlchemy, Ultralytics, OpenCV, PyTorch, and related dependencies.

Features
AI-based weld defect detection
Image-based inspection
Recorded-video detection
YOLO bounding-box detection
Defect classification
Confidence score display
PASS/FAIL inspection status
Inspection history
Inspection statistics
Annotated inspection images
Processing-time measurement
Interactive React dashboard
REST API using FastAPI
System Architecture
                 Weld Image / Video
                         |
                         v
                  YOLO Model
                 Defect Detection
                         |
                         v
                   FastAPI
                    Backend
                         |
                +--------+--------+
                |                 |
                v                 v
            Database        React Dashboard
          SQLite/MySQL
Project Structure
PRJ_65/
|
├── backend/
│   └── main.py
|
├── frontend/
│   ├── src/
│   ├── package.json
│   └── ...
|
├── models/
│   └── weld_defect.pt
|
├── training/
│   ├── train.py
│   └── evaluate.py
|
├── realtime/
│   └── video_detection.py
|
├── dataset/
│   └── DOWNLOAD.md
|
├── sql/
│   └── schema.sql
|
├── outputs/
│   ├── annotated_weld.mp4
│   └── annotated_weld.jsonl
|
├── requirements.txt
├── .env.example
└── README.md

The project uses a trained model at models/weld_defect.pt. Without the model weights, the inspection endpoint returns HTTP 503 instead of presenting a false PASS result.

Installation
1. Create a Virtual Environment

Open PowerShell in the project directory:

python -m venv .venv

Activate the environment:

.\.venv\Scripts\Activate.ps1
2. Install Dependencies
python -m pip install --upgrade pip
pip install -r requirements.txt
3. Configure Environment Variables
Copy-Item .env.example .env

The project uses SQLite by default, so MySQL is not required for the basic setup.

Running the Backend

Activate the virtual environment:

.\.venv\Scripts\Activate.ps1

Start FastAPI:

uvicorn backend.main:app --reload

Backend:

http://127.0.0.1:8000

FastAPI documentation:

http://127.0.0.1:8000/docs

Health check:

http://127.0.0.1:8000/health

The supplied project logs confirm that the FastAPI backend starts successfully on port 8000.

Running the React Dashboard

Open another PowerShell terminal:

cd frontend
npm install
npm run dev

The Vite development server normally runs at:

http://localhost:5173

The supplied project logs show the React/Vite frontend running successfully on port 5173.

API Endpoints
Method	Endpoint	Purpose
POST	/inspect	Upload an image for inspection
GET	/inspections	Retrieve recent inspections
GET	/inspections/{inspection_id}	Retrieve a specific inspection
GET	/statistics	Retrieve inspection statistics
GET	/health	Check API/model readiness

The /inspect endpoint accepts an image and optional weld/camera identifiers. Inspection records contain defect type, confidence, bounding box, PASS/FAIL status, processing time, and annotated image information.

YOLO Model

The YOLO model analyzes the weld image and identifies defects using object detection.

The intended classes are:

0 → Crack
1 → Porosity
2 → Undercut

The project uses fine-tuning of a pretrained YOLO model rather than training a model completely from scratch.

After training, the best model is placed at:

models/weld_defect.pt
Real-Time and Video Inspection

A recorded weld video can be processed using:

python realtime/video_detection.py path\to\weld_video.mp4

The system generates:

outputs/annotated_weld.mp4
outputs/annotated_weld.jsonl

The detector can later be connected to an industrial camera instead of a recorded video source.

Database
SQLite

SQLite is the default database for development and demonstration.

MySQL

MySQL can be used for production configuration.

Example:

DATABASE_URL=mysql+pymysql://root:YOUR_PASSWORD@localhost:3306/weld_inspection

After configuring MySQL, restart the FastAPI backend so inspection data can be persisted to MySQL.

Dashboard

The React dashboard provides an interface for:

Viewing inspection results
Checking PASS/FAIL status
Viewing detected defects
Viewing confidence values
Reviewing inspection history
Viewing statistics
Viewing annotated inspection images

The backend provides inspection-history and statistics APIs that are used by the dashboard.

Model Evaluation

Run the evaluation script:

python training/evaluate.py

The evaluation provides:

Precision
Recall
mAP50
mAP50-95
Confusion Matrix

The evaluation uses a held-out test split.

SDG Relevance
SDG 9 — Industry, Innovation and Infrastructure

The project applies computer vision and AI to manufacturing quality-control workflows.

SDG 12 — Responsible Consumption and Production

Early detection of weld defects can help reduce rework, material waste, rejected parts, and defective output.

Future Scope
Integration with live industrial cameras.
Real-time production-line inspection.
Improved model accuracy using larger datasets.
Addition of more weld-defect classes.
Cloud-based inspection history.
Automated quality-control reports.
Industrial IoT integration.
Deployment on edge devices for low-latency inspection.
Project Summary

This project demonstrates the integration of Artificial Intelligence, Computer Vision, REST APIs, databases, and modern web technologies into an automated industrial inspection system.

Computer Vision + YOLO
          |
          v
     FastAPI Backend
          |
          v
       Database
          |
          v
    React Dashboard
License

This project is developed for academic and educational demonstration purposes.
