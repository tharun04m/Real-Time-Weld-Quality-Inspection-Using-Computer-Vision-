# PRJ_65 — Real-Time Weld Quality Inspection

College demonstration system: **weld image/video → YOLO → metadata → FastAPI → database → React dashboard**. It recognizes the intended YOLO class IDs: `0 crack`, `1 porosity`, and `2 undercut`.

## 1. Setup

In PowerShell, from this folder:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
Copy-Item .env.example .env
```

The first PyTorch installation can take time. The application defaults to SQLite so it works without installing MySQL.

## 2. Start the backend

```powershell
.\.venv\Scripts\Activate.ps1
uvicorn backend.main:app --reload
```

Visit `http://127.0.0.1:8000/docs` for interactive API documentation and `http://127.0.0.1:8000/health` to check the service.

## 3. Start the dashboard

Open another PowerShell window:

```powershell
cd frontend
npm install
npm run dev
```

Open the URL Vite prints, normally `http://localhost:5173`.

## 4. Add the trained model

Until `models/weld_defect.pt` exists, `POST /inspect` returns HTTP 503 and does not record a result. This prevents a false `PASS` from being presented as an AI inspection. Obtain an academic-use public dataset, annotate in YOLO format, then fine-tune pretrained YOLO (never train from scratch):

```powershell
.\.venv\Scripts\Activate.ps1
python training/train.py
Copy-Item models\weld_training\weights\best.pt models\weld_defect.pt
python training/evaluate.py
```

Review the generated `results.png` and `confusion_matrix.png` under `models/weld_training`: report precision, recall, mAP50, and mAP50-95 from validation. `training/evaluate.py` uses the held-out test split. Inference time is returned per inspection in `processing_time_ms`.

Download the selected matching CC BY 4.0 dataset and prepare `dataset/images/{train,val,test}` with matching label folders using [dataset/DOWNLOAD.md](dataset/DOWNLOAD.md). Use its supplied split (or about 70/20/10 when creating your own split).

## 5. Recorded-video demo

```powershell
python realtime/video_detection.py path\to\weld_video.mp4
```

This creates `outputs/annotated_weld.mp4` and per-frame metadata in `outputs/annotated_weld.jsonl`. Later an industrial camera can replace `cv2.VideoCapture(video)` with its camera capture source; the detector interface remains the same.

## 6. MySQL (optional production configuration)

Create the MySQL database/table by running [sql/schema.sql](sql/schema.sql), install a local MySQL Community Server, then set this in `.env`:

```text
DATABASE_URL=mysql+pymysql://root:YOUR_PASSWORD@localhost:3306/weld_inspection
```

Restart FastAPI. The same inspection APIs then persist to MySQL.

## APIs

| Endpoint | Purpose |
|---|---|
| `POST /inspect` | Upload image; fields: `image`, optional `weld_id`, `camera_id` |
| `GET /inspections` | Recent persisted metadata |
| `GET /inspections/{inspection_id}` | Metadata for one inspection |
| `GET /statistics` | Dashboard totals and defect counts |
| `GET /health` | API/model readiness |

Each detection contains ID, timestamp, camera/weld ID, defect type/confidence, bounding box (`x`, `y`, `width`, `height`), PASS/FAIL, processing time, and annotated image URL. Defect-free images produce a single `PASS` record with null defect fields.

## Verification performed

The health and statistics endpoints can be checked without model weights. After adding trained weights, use an image upload to verify the full pipeline creates an annotated image and persisted metadata. The dashboard production build completes successfully. Run `python tests/create_sample.py` to recreate a synthetic input; it is not a defect-training image.

## SDG relevance

**SDG 9 — Industry, Innovation and Infrastructure:** automated visual inspection brings accessible computer vision into manufacturing quality-control workflows. **SDG 12 — Responsible Consumption and Production:** finding weld defects early reduces rework, material waste, rejected parts, and unsafe product output.
