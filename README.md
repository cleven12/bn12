# Student Performance Prediction System
## Using Data Analytics and Machine Learning

**Institution:** Mwenge Catholic University (MWECAU)  
**Department:** Natural Sciences and Information Technology  
**Programme:** Bachelor of Science in Computer Science  
**Year:** 2026

**Team Members**
| Name | Registration Number |
|------|-------------------|
| Laurent Boniphace | T/DEG/2023/1445 |
| Christina Samwel | T/DEG/2023/0919 |
| Elizabeth Michael | T/DEG/2023/0818 |

---

## 1. Purpose

This system enables academic staff at MWECAU to predict student academic risk early — before final examinations — so that timely interventions can be made. It analyses Continuous Assessment (CA) scores, attendance, and demographic data using machine learning to classify each student as **Safe**, **At-Risk**, or **High Risk**.

---

## 2. MWECAU Grading Standards

### 2.1 Grade Scale (per subject)

| Score Range | Grade Points | Grade | Remark      |
|-------------|-------------|-------|-------------|
| 75 – 100    | 5           | A     | Distinction  |
| 65 – 74     | 4           | B+    | High Credit  |
| 55 – 64     | 3           | B     | Credit       |
| 45 – 54     | 2           | C     | Pass         |
| 35 – 44     | 1           | D     | Fail         |
| 0  – 34     | 0           | E     | Bad Fail     |

### 2.2 GPA & Degree Classification

| GPA Range | Classification       |
|-----------|---------------------|
| 4.5 – 5.0 | First Class          |
| 3.5 – 4.4 | Upper Second Class   |
| 2.6 – 3.4 | Lower Second Class   |
| 2.0 – 2.5 | Pass                 |

### 2.3 Score Weight Formula

```
CA Score    = quiz + assignment + practical + presentation + attendance + test  (max 100)
CA Weighted = CA Score × 0.40   (40%)
Exam Weighted = Exam Score × 0.60   (60%)
Final Score = CA Weighted + Exam Weighted
```

### 2.4 Risk Label Derivation (ML Target Variable)

| Final Score | Grade | Risk Label  |
|-------------|-------|-------------|
| 75 – 100    | A     | Safe        |
| 65 – 74     | B+    | Safe        |
| 55 – 64     | B     | Safe        |
| 45 – 54     | C     | At-Risk     |
| 35 – 44     | D     | High Risk   |
| 0  – 34     | E     | High Risk   |

---

## 3. Data Model

Each student record contains the following fields:

| Field | Description | Max Score |
|-------|-------------|-----------|
| reg_number | Student registration number | — |
| gender | Male / Female | — |
| year_of_study | 1, 2, or 3 | — |
| course_code | e.g. CSC311 | — |
| quiz | Quiz score | 10 |
| assignment | Assignment score | 20 |
| practical | Practical score | 20 |
| presentation | Presentation score | 20 |
| attendance | Attendance score | 10 |
| test | Test score | 20 |
| ca_total | Sum of CA components | 100 |
| ca_weighted | ca_total × 0.40 | 40 |
| exam_score | Final exam score | 100 |
| exam_weighted | exam_score × 0.60 | 60 |
| final_score | ca_weighted + exam_weighted | 100 |
| grade | A / B+ / B / C / D / E | — |
| gpa_points | 0 – 5 | 5 |
| risk_label | Safe / At-Risk / High Risk | — |

---

## 4. Machine Learning Approach

### 4.1 Two Prediction Modes

| Mode | Input Features | Purpose |
|------|---------------|---------|
| **Early (pre-exam)** | CA components only: quiz, assignment, practical, presentation, attendance, test, ca_total, ca_weighted | Mid-semester early warning — intervene before exams |
| **Full (post-exam)** | All fields including exam_score and final_score | End-of-semester analysis and reporting |

### 4.2 Algorithms Implemented

| Algorithm | Purpose |
|-----------|---------|
| Logistic Regression | Baseline — fast, probabilistic output |
| Decision Tree | Interpretable rules — easy to explain to educators |
| Random Forest | Primary model — highest accuracy through ensemble voting |

### 4.3 Model Evaluation Metrics

Each trained model is evaluated and reported on:
- **Accuracy** — overall correct predictions
- **Precision** — of students flagged at-risk, how many truly were
- **Recall** — of truly at-risk students, how many were caught
- **F1-Score** — harmonic mean of precision and recall

---

## 5. System Architecture

```
┌─────────────────────────────────────────────────────┐
│                   Browser (User)                    │
└────────────────────┬────────────────────────────────┘
                     │ HTTP
┌────────────────────▼────────────────────────────────┐
│              Flask Web Application                  │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐            │
│  │Dashboard │ │Students  │ │  ML      │            │
│  │  Routes  │ │  Routes  │ │  Routes  │            │
│  └──────────┘ └──────────┘ └──────────┘            │
│                     │                               │
│  ┌──────────────────▼──────────────────────┐        │
│  │         ML Pipeline                     │        │
│  │  generate_data → train → predict        │        │
│  │  (scikit-learn: LR, DT, Random Forest)  │        │
│  └──────────────────┬──────────────────────┘        │
└─────────────────────┼───────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────┐
│                MySQL Database                       │
│         users │ students │ predictions              │
└─────────────────────────────────────────────────────┘
```

---

## 6. Web Pages

| Page | URL | Description |
|------|-----|-------------|
| Login | `/login` | Secure admin access |
| Dashboard | `/` | Summary cards + 3 Chart.js charts |
| Students | `/students` | Searchable, filterable student table |
| Add Student | `/students/add` | Manual entry form with validation |
| Bulk Upload | `/students/upload` | Import students from CSV file |
| Predict | `/predict` | Enter scores → instant risk prediction |
| Train Models | `/train` | Trigger ML training + view model metrics |

### Dashboard Charts
- Pie chart: overall Safe / At-Risk / High Risk distribution
- Bar chart: risk count by year of study (Year 1, 2, 3)
- Bar chart: average final score by course code

---

## 7. Project File Structure

```
student_perf_prdct/
├── app.py                   # Main Flask application
├── config.py                # Configuration (DB, secret key)
├── models.py                # Database models (SQLAlchemy)
├── schema.sql               # MySQL table definitions
├── requirements.txt         # Python dependencies
│
├── ml/
│   ├── generate_data.py     # Synthetic training data generator
│   ├── train.py             # Model training and evaluation
│   └── predict.py           # Prediction logic
│
├── templates/               # HTML templates (Jinja2 + Bootstrap 5)
│   ├── base.html
│   ├── login.html
│   ├── dashboard.html
│   ├── students.html
│   ├── student_form.html
│   ├── upload.html
│   └── predict.html
│
├── static/
│   └── style.css
│
├── data/
│   └── student_data.csv     # Combined dataset (~1000 rows)
│
└── saved_models/            # Trained model files (.pkl)
```

---

## 8. Technology Stack

| Layer | Technology |
|-------|-----------|
| Programming Language | Python 3.11 |
| Web Framework | Flask 3.x |
| Database | MySQL 8 |
| ORM | Flask-SQLAlchemy + PyMySQL |
| Machine Learning | scikit-learn 1.5, pandas, numpy, joblib |
| Authentication | Flask-Login + Werkzeug |
| Frontend | Bootstrap 5 + Chart.js (CDN) |
| Templates | Jinja2 |

All technologies are **free and open-source**. No paid licences required.

---

## 9. Development Sprints

| Sprint | Focus | Key Deliverables |
|--------|-------|-----------------|
| 1 — Setup | Project scaffold & data | MySQL schema, synthetic dataset (1000 rows with MWECAU grading) |
| 2 — ML Pipeline | Model development | Trained LR, DT, RF models; evaluation metrics; .pkl files saved |
| 3 — Web UI | Full interface | All 7 pages, CSV upload, Chart.js dashboard, prediction forms |
| 4 — Testing | Validation & polish | Unit tests, form validation, UAT with sample data |

---

## 10. How to Run

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Set up database
mysql -u root -p < schema.sql

# 3. Generate training data and train models
python ml/generate_data.py
python ml/train.py

# 4. Start the application
python app.py
# Open: http://localhost:5000
# Default login: admin / admin123
```

---

## 11. Ethical Considerations

- Student data is stored with **encrypted passwords** and **access control** (login required)
- Registration numbers are used instead of personal names in ML features
- Predictions are **advisory only** — final decisions remain with academic staff
- Data handling complies with institutional privacy policies and informed consent requirements
