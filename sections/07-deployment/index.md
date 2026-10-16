---
title: Deployment
has_children: false
nav_order: 8
---

# Deployment

MeetNote is a local desktop application. It does not require a dedicated
server, cloud database, or web hosting service. The application runs directly
on the user’s machine and stores its data locally.

## User installation

To run MeetNote, the user needs Python installed on their computer. The project
supports Python versions from 3.11 to 3.13.

The recommended way to install and run the application is through Poetry.

### 1. Clone the repository

```bash
git clone [https://github.com/unibo-dtm-se-2526-MEETNOTE/Artifact.git](https://github.com/unibo-dtm-se-2526-MEETNOTE/Artifact.git)
cd Artifact
```

### 2. Install dependencies

On Windows, run:

```powershell
py -3.12 -m poetry install
```

On macOS or Linux, run:

```bash
poetry install
```

This installs the main dependencies, including Kivy and the Google Gemini
Python library.

### 3. Configure the application

MeetNote can use either a fake extractor or the Gemini API to extract action
items.

To use Gemini, set the `GEMINI_API_KEY` environment variable before starting
the application.

On Windows PowerShell:

```powershell
$env:GEMINI_API_KEY="your_gemini_api_key"
$env:MEETNOTE_ACTION_EXTRACTOR="gemini"
```

On macOS or Linux:

```bash
export GEMINI_API_KEY="your_gemini_api_key"
export MEETNOTE_ACTION_EXTRACTOR="gemini"
```

The application reads the API key from the environment and does not require it
to be written in the source code. If Gemini is selected but `GEMINI_API_KEY` is
missing, MeetNote raises a clear error before initializing the extractor.

By default, MeetNote uses the fake transcriber and fake action-item extractor.
These implementations are useful for testing the application without processing
a real recording or calling an external AI service.

### 4. Optional: enable local Whisper transcription

By default, MeetNote uses `FakeTranscriber`. To transcribe a real audio or
video file locally, the optional Whisper dependencies must be installed.

Install them with:

```powershell
py -3.12 -m poetry install --extras whisper
```

Or, on macOS or Linux:

```bash
poetry install --extras whisper
```

Then set the transcriber environment variable:

```powershell
$env:MEETNOTE_TRANSCRIBER="whisper"
```

Or:

```bash
export MEETNOTE_TRANSCRIBER="whisper"
```

When Whisper is enabled, MeetNote uses the local `base` Whisper model on the
CPU with `int8` computation.

### 5. Run the application

On Windows, run:

```powershell
py -3.12 -m poetry run python -m meetnote
```

On macOS or Linux, run:

```bash
poetry run python -m meetnote
```

This starts the Kivy desktop application.

## Using the application

After MeetNote starts:

1. Enter a meeting title.
2. Select an audio or video file.
3. Click **Submit meeting**.
4. Wait for the transcription process to finish.
5. Click **Extract action items** to identify tasks from the transcript.
6. Click **Generate meeting summary** to obtain a summary of the meeting.
7. Click **Open Kanban board** to manage the extracted action items.

The application supports the following audio and video formats:

- Audio: `.mp3`, `.wav`, `.m4a`, `.flac`, `.ogg`
- Video: `.mp4`, `.mov`, `.avi`

## Server-side installation

MeetNote does not require a server-side installation. There is no backend
server, web application server, message broker, or external database.

All application data is stored locally in an SQLite database. The application
creates a `data` directory and stores the database as:

```text
data/meetnote.db
```

The database contains two tables:

- `meetings`, which stores meeting titles, notes, transcripts, and creation
  timestamps.
- `action_items`, which stores action-item descriptions, owners, deadlines,
  statuses, and their relationship to meetings.

Because the database is local, deleting the `data` directory removes the
application’s locally stored meetings and action items.

## PyPI installation

The project is also configured to publish the `meetnote` Python package to
PyPI through its automated release workflow.

If the package is available on PyPI, it can be installed with:

```bash
pip install meetnote
```

However, the recommended way to run the current version of the project during
development is to clone the repository and use Poetry, as described above.