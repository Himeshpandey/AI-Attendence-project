#   ai-attendance-project-app 
# SnapClass: AI-Powered Attendance System

SnapClass is a web app that takes classroom attendance with face recognition and voice recognition. Teachers upload classroom photos or use voice attendance. Students log in with FaceID and track their own attendance.

Built with Python and Streamlit, using dlib-based face embeddings, Resemblyzer voice embeddings and Supabase for storage.

---

## Features

### Teacher portal
- Register and log in with a username and password (hashed with bcrypt).
- Create subjects, each with a subject code and section.
- Share a subject through a join code or a QR code (segno).
- Upload one or more classroom photos and run face analysis to mark enrolled students present or absent.
- Use voice attendance as an alternative to photos.
- Review the results, then save them to the database.
- View attendance records grouped by session, with present/total counts.

### Student portal
- Log in with FaceID using the webcam, with no password.
- Register a new profile from a face capture, with optional voice enrollment.
- Enroll in or unenroll from subjects, using a code or a join link (`?join-code=<code>`).
- View per-subject totals and classes attended.

### AI / ML
- Face detection and 128-d face embeddings via `dlib` and `face_recognition_models`.
- A classifier trained on stored student embeddings (scikit-learn). It is retrained whenever a new student registers.
- Voice embeddings via `resemblyzer` and `librosa`.
- Multi-photo aggregation: a student detected in any uploaded photo is marked present, and the source photo is recorded.
- The login flow checks that exactly one face is in frame.

---

## Architecture

```
Streamlit UI (app.py)
 ├── Screens: home / teacher / student
 ├── Components: dialogs (enroll, create subject, share, add photo, results, voice)
 ├── Pipelines
 │    ├── face_pipeline   → embeddings, classifier training, predict_attendance
 │    └── voice_pipeline  → voice embeddings
 └── Database layer (Supabase)
      └── teachers, students, subjects, subject_students, attendance logs
```

---

## Project structure

```
AI-Attendence-project/
├── app.py              # Entry point and role-based routing
├── requirements.txt    # Python dependencies
├── packages.txt        # System packages (for Streamlit Cloud / Linux)
├── .gitignore
└── src/
    ├── screens/        # home_screen, teacher_screen, student_screen
    ├── components/     # dialogs, header, footer, subject_card
    ├── pipelines/      # face_pipeline, voice_pipeline
    ├── database/       # Supabase client (config) and queries (db)
    └── ui/             # Layout and styling helpers
```

---

## Tech stack

| Area | Tools |
|---|---|
| Frontend / app | Streamlit |
| Face recognition | dlib, face_recognition_models, scikit-learn |
| Voice recognition | Resemblyzer, librosa |
| Data | NumPy, pandas |
| Backend / DB | Supabase |
| Security | bcrypt |
| Utilities | segno (QR codes), Pillow |

---

## Getting started

### Prerequisites
- Python 3.10 or later (the code uses `match` statements)
- A Supabase project
- A webcam and a microphone
- Build tools for dlib:
  - Ubuntu/Debian: `sudo apt install cmake build-essential libopenblas-dev liblapack-dev libx11-dev`
  - macOS: `brew install cmake`
  - Windows: install CMake and Visual Studio Build Tools

### Installation

```bash
git clone https://github.com/Himeshpandey/AI-Attendence-project.git
cd AI-Attendence-project

python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

pip install -r requirements.txt
```

### Configuration

Add your Supabase credentials. The exact variable names are defined in `src/database/config.py`. For Streamlit, create `.streamlit/secrets.toml`:

```toml
SUPABASE_URL = "<your-supabase-url>"
SUPABASE_KEY = "<your-supabase-key>"
```

Create the tables the app uses (teachers, students, subjects, subject_students, attendance) in Supabase. Student rows store a face embedding and an optional voice embedding.

### Run

```bash
streamlit run app.py
```

---

## Usage

1. **Teacher:** register, log in, then create a subject and share its code.
2. **Student:** open the Student Portal and register with a face capture, then optionally record a voice sample. Join the subject with the code or link.
3. **Take attendance:** under *Take Attendance*, choose a subject, add classroom photos and click *Run Face Analysis*. Review the results and save them. You can also click *Use Voice Attendance*.
4. **Review:** open *Attendance Records* (teacher) or the dashboard (student).

---

## How it works

1. **Enrollment:** a face image is converted to an embedding and stored with the student record. The classifier is retrained.
2. **Recognition:** each classroom photo is scanned for faces, the embeddings are matched to enrolled students, and detections are merged across photos.
3. **Attendance logging:** every enrolled student gets a present/absent row with a timestamp for that subject.

---

## Limitations and future work

- Accuracy depends on lighting, resolution and face angle.
- Add liveness detection to prevent photo-spoofing at FaceID login.
- Add model evaluation metrics (precision, recall, confusion matrix) on a held-out set.
- Add CSV/Excel export of attendance.
- Add unit tests and a CI pipeline.
- Encrypt stored biometric embeddings and add a consent and privacy policy.

---

## Privacy note

This app stores biometric data (face and voice embeddings). Obtain consent from users, secure your database with Row Level Security, and follow applicable data-protection laws.

---

## Author

**Himesh Pandey**: [GitHub](https://github.com/Himeshpandey)

## License

<Add a license, e.g. MIT>
