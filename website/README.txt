IAB207 – Event Management System

A Flask web app for browsing events, booking tickets, posting comments, and creating/cancelling events.

Tech: Python, Flask, Flask-Login, Flask-SQLAlchemy, Flask-WTF, Bootstrap

Entry point: main.py

App package: website/

Packaged demo database (ships with repo): website/website/_data/app.db

The app boots with ready-made demo data from the packaged SQLite DB so the tutor/marker can run it with zero setup and immediately see events and users.

1) Quick start
Prereqs

Python 3.10 – 3.13

Git

Clone & run

Windows (Git Bash / PowerShell):

git clone <your-team-repo-url> a2_groupX
cd a2_groupX

python -m venv venv
# Git Bash:
source venv/Scripts/activate
# PowerShell (alternative):
# .\venv\Scripts\Activate.ps1

pip install -r requirements.txt

# Run the app
flask --app main.py run


macOS / Linux:

git clone <your-team-repo-url> a2_groupX
cd a2_groupX

python3 -m venv venv
source venv/bin/activate

pip install -r requirements.txt
flask --app main.py run


Visit: http://127.0.0.1:5000

You should see the landing page with upcoming events. You can Register/Login, comment, book tickets, and create/cancel your own events.

2) Configuration (.env)

A minimal .env is supported (optional):

FLASK_SECRET_KEY=change-me
# Leave DATABASE_URL unset to use the packaged demo DB automatically.
# If you REALLY need to override:
# DATABASE_URL=sqlite:///absolute/or/relative/path/to/some.db


Important: Do not set DATABASE_URL unless you intentionally want to point at a different database. By default we auto-select the packaged DB at website/website/_data/app.db.

3) Database model & where data lives

We ship a SQLite file at: website/website/_data/app.db (tracked in Git)

On first run, the app prefers:

DATABASE_URL (if set) otherwise

Packaged DB at website/website/_data/app.db otherwise

Local instance/app.db

Initialize / seed (only if you need to rebuild or local test)

We provide CLI commands:

# Create all tables against the CURRENT configured DB
flask --app main.py init-db

# Seed sample users/events/comments (idempotent)
flask --app main.py seed-db


Markers/Tutors don’t need to run these. They can just run the app and see the demo data immediately.

Resetting the packaged demo DB (dev-only)

If you ever corrupt your local copy and want the repo version again:

git restore --source=HEAD website/website/_data/app.db

4) Project structure (key parts)
a2_groupX/
  main.py                # creates the Flask app (factory) and exposes 'app'
  requirements.txt
  website/
    __init__.py          # app factory, DB config, CLI commands (init-db, seed-db)
    models.py            # User, Event, Booking, Comment
    forms.py             # WTForms: Register, Login, Event, Booking, Comment
    routes.py            # Blueprints / views
    templates/
      base.html          # Navbar, layout
      index.html         # Landing page w/ event list
      event_detail.html  # Event page + comments + booking form
      create_event.html  # Create event form
      booking_history.html
      404.html           # Error handler
      500.html           # Error handler
    static/              # CSS, images (if any)
    website/_data/app.db # Packaged demo database (tracked)
instance/                # Local-only DB if you choose to use it (ignored by Git)
venv/                    # Your virtualenv (do not commit)

5) What the app can do (per assessment)

Landing page shows events (with status: Open, Inactive, Sold Out, Cancelled)

Browse by category (if present), optional text search (if implemented)

Anyone can view event details (image, description, date, etc.)

Auth: register, login, logout (with name, email, password, phone, address)

Bookings: logged-in users can book tickets; booking history reflects actual data

Comments: logged-in users can post comments; visible to everyone

Create/Update/Cancel events: owners manage their events (status derived from rules)

Errors: Custom 404 & 500 pages

All pages: navbar + DB-backed dynamic data

6) Contribution workflow (team)

Pull latest

git checkout main
git pull


Create a feature branch

git checkout -b feature/<short-name>


Do your work, commit early/often

git add -A
git commit -m "Implement X"
git push -u origin feature/<short-name>


Open a Pull Request → teammate reviews → squash & merge to main.

Please do not commit venv/, instance/, or secrets. The packaged DB we do track lives at website/website/_data/app.db.

7) Troubleshooting
“unable to open database file”

You probably pointed DATABASE_URL to a bad path. Unset it:

# Git Bash
unset DATABASE_URL


Or your path uses Windows backslashes. Use forward slashes in URIs:

sqlite:///C:/Users/you/path/to/app.db

“no such table: …”

Run:

flask --app main.py init-db
flask --app main.py seed-db

Git can’t add the DB

We intentionally ignore *.db globally but allow website/**/_data/app.db.

If needed, force add:

git add -f website/website/_data/app.db

Windows notes

Always activate the venv before running Flask:

source venv/Scripts/activate


If PowerShell blocks scripts: run PowerShell as admin and:

Set-ExecutionPolicy -Scope CurrentUser RemoteSigned

8) Deployment (for Week 13 demo)

Target: PythonAnywhere

Copy repo, create a virtualenv there, pip install -r requirements.txt.

Point the WSGI app to main.py:app.

Ensure the working directory is the repo root so the packaged DB resolves.

(Optional) If PythonAnywhere storage is read-only in your plan, the packaged DB will display demo data but not persist writes; that’s fine for the demo. Otherwise, it will persist in place.

9) Academic integrity

Per the unit instructions, use of generative AI for shipped code/writing is restricted. Any learning aids should not appear verbatim in the submission. Each member should understand and be able to explain their code.

10) QA checklist before submitting

 flask --app main.py run works with no environment setup

 Home page shows events from the packaged DB

 Register / Login / Logout works

 Create event / Cancel / Update works

 Book tickets and Booking history updates

 Commenting works and shows name + timestamp

 404 and 500 custom pages render

 Final ZIP structure matches spec:

a2_groupX/
  Assignment_declaration.docx
  main.py
  requirements.txt
  website/...


(No venv/, no huge files)