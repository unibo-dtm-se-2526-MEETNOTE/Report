---
title: Requirements
has_children: false
nav_order: 3
---

# Requirements

## User stories

MeetNote is designed for people who attend meetings, record them, and need to
turn the discussion into concrete follow-up work. The application currently
does not implement a login system, so it is intended as a local desktop
application used by an individual user or shared on a single machine.

### Meeting participant

- As a meeting participant, I want to upload an audio or video recording of a meeting so that I do not have to take detailed notes manually.
- As a meeting participant, I want MeetNote to transcribe the recording so that I can read what was discussed.
- As a meeting participant, I want the application to identify explicit action items from the transcript so that I can remember what needs to be done.
- As a meeting participant, I want to see who is responsible for an action item when this information is stated in the meeting.
- As a meeting participant, I want to see deadlines associated with action items so that I know when work should be completed.
- As a meeting participant, I want to generate a meeting summary so that I can quickly understand the main points, decisions, and follow-ups.

### Meeting organizer

- As a meeting organizer, I want to view all action items on a Kanban board so that I can follow the progress of the meeting outcomes.
- As a meeting organizer, I want to mark an action item as done so that the team can see which work has been completed.
- As a meeting organizer, I want to verify a completed action item so that I can confirm that the work was actually completed.
- As a meeting organizer, I want to reopen an action item if it was completed incorrectly or needs further work.
- As a meeting organizer, I want to delete an incorrect or unnecessary action item.
- As a meeting organizer, I want to see upcoming deadlines for open action items so that I can prioritize urgent work.

## Requirements analysis

The following requirements describe what MeetNote should do, independently of
the specific implementation choices. The application processes a meeting
recording, produces a transcript and meeting summary, extracts explicit action
items, and allows the user to manage those items through a Kanban board.

## Functional requirements

### FR1 — Meeting creation

The system shall allow the user to create a meeting by providing a title and
selecting an audio or video recording.

**Acceptance criteria:**

- The user can enter a meeting title.
- The user can select an audio or video file from their device.
- The system rejects an empty meeting title.
- The system rejects a submission when no recording has been selected.
- The system creates a new meeting and shows its identifier to the user.

### FR2 — Recording selection

The system shall allow the user to select a supported audio or video file.

**Acceptance criteria:**

- The user can browse their local file system.
- The system accepts audio files with extensions `.mp3`, `.wav`, `.m4a`, `.flac`, and `.ogg`.
- The system accepts video files with extensions `.mp4`, `.mov`, and `.avi`.
- The system shows the selected file path.
- The system shows an error message if the user tries to select a folder instead of a file.

### FR3 — Transcription

The system shall transcribe the selected meeting recording into text.

**Acceptance criteria:**

- After a meeting is submitted, the system starts the transcription process.
- The system shows the user that transcription is in progress.
- When transcription finishes, the system displays the generated transcript.
- The transcript is associated with the corresponding meeting.
- If transcription fails, the system shows a clear error message.

### FR4 — Action-item extraction

The system shall extract explicit action items from the meeting transcript.

**Acceptance criteria:**

- The user can request action-item extraction after a meeting has been submitted.
- The system extracts only explicit and concrete tasks stated in the transcript.
- The system does not invent action items when no clear task is present.
- Each extracted action item contains a description.
- When explicitly stated in the transcript, an action item includes a responsible person.
- When explicitly stated in the transcript, an action item includes a deadline in `YYYY-MM-DD` format.
- The system shows the number of extracted action items.
- The user can refresh the list of action items.

### FR5 — Meeting-summary generation

The system shall generate a concise summary of the meeting from its transcript.

**Acceptance criteria:**

- The user can request a meeting summary after a meeting has been submitted.
- The summary includes the main discussion points, decisions, and follow-ups when they are present in the transcript.
- The summary does not include information that is not present in the transcript.
- The generated summary is displayed to the user.
- If summary generation fails, the system shows a clear error message.

### FR6 — Kanban board

The system shall display the action items of a meeting in a Kanban board.

**Acceptance criteria:**

- The Kanban board contains three columns: `Open`, `Done`, and `Verified`.
- Each action item is displayed in the column corresponding to its current status.
- Each card shows the action-item description, owner, and deadline.
- If an action item has no owner, the card shows `All / unassigned`.
- If an action item has no deadline, the card shows `No deadline`.
- The user can refresh the board.

### FR7 — Action-item status management

The system shall allow the user to change the status of an action item.

**Acceptance criteria:**

- An open action item can be marked as done.
- A done action item can be verified.
- A done or verified action item can be reopened.
- After a status change, the Kanban board is updated.
- The board shows the number of open, done, and verified action items.

### FR8 — Action-item deletion

The system shall allow the user to delete an action item.

**Acceptance criteria:**

- The user can delete an action item from the Kanban board.
- After deletion, the action item is no longer displayed on the board.
- The board is refreshed after deletion.

### FR9 — Upcoming deadlines

The system shall show upcoming deadlines for open action items.

**Acceptance criteria:**

- The system lists open action items that have a deadline.
- Deadlines are sorted in ascending order.
- Each deadline entry shows the due date, action-item description, and owner.
- If no open action item has a deadline, the system shows that there are no upcoming deadlines.

### FR10 — Data persistence

The system shall persist meetings and action items between application sessions.

**Acceptance criteria:**

- Meetings are stored locally by the application.
- Action items are stored locally and associated with their meeting.
- A user can return to a previously created meeting and view its action items.
- The application creates its local data directory if it does not already exist.

## Non-functional requirements

### NFR1 — Usability

The application shall provide a simple desktop interface that allows a user to
complete the main workflow without technical knowledge.

**Acceptance criteria:**

- The user can select a recording, submit a meeting, extract action items, generate a summary, and open the Kanban board from the main interface.
- The application shows the current processing status to the user.
- Error messages describe what went wrong in understandable language.

### NFR2 — Responsiveness

The application shall avoid freezing the user interface during long-running
operations.

**Acceptance criteria:**

- Transcription, action-item extraction, and summary generation run in the background.
- The user interface remains responsive while a long-running operation is in progress.
- The application prevents the same long-running operation from being started again while it is already running.

### NFR3 — Security

The application shall not require sensitive credentials to be hard-coded in the
source code.

**Acceptance criteria:**

- The Gemini API key is read from the `GEMINI_API_KEY` environment variable.
- The application does not start the Gemini extractor when the required API key is missing.
- The application raises a clear error when Gemini is selected but `GEMINI_API_KEY` is not configured.

### NFR4 — Reliability

The application shall handle missing files and failed external operations
gracefully.

**Acceptance criteria:**

- The application reports an error if the selected audio file does not exist.
- The application reports an error if transcription fails.
- The application reports an error if action-item extraction fails.
- The application reports an error if meeting-summary generation fails.
- The application does not silently lose user data when an operation fails.

### NFR5 — Maintainability

The application shall separate its user interface, application logic, domain
model, and storage components.

**Acceptance criteria:**

- The user interface does not directly implement persistence or external-service logic.
- Transcription and action-item extraction are defined through replaceable interfaces.
- The application can use a fake transcriber or extractor for development and testing.
- The codebase includes static analysis and test tooling.

## Implementation requirements

### IR1 — Programming language

The application shall be implemented in Python.

**Justification:** Python is suitable for rapid application development and
provides libraries for desktop graphical interfaces, local transcription, and
integration with the Gemini API.

### IR2 — Desktop user interface

The application shall use Kivy for its graphical user interface.

**Justification:** Kivy allows the development of a desktop application with a
graphical interface directly in Python. It provides the components needed for
file selection, forms, labels, buttons, pop-ups, and multiple screens.

### IR3 — Local transcription

The application shall support local transcription using `faster-whisper`.

**Justification:** Local transcription allows meeting recordings to be processed
without sending the audio file to an external transcription service. The
application uses the Whisper `base` model on CPU with `int8` computation.

### IR4 — AI analysis

The application shall support action-item extraction and meeting-summary
generation through the Google Gemini API.

**Justification:** Gemini provides natural-language understanding capabilities
that can identify explicit action items and summarize unstructured meeting
transcripts.

### IR5 — Data storage

The application shall store meetings and action items in a local SQLite
database.

**Justification:** SQLite is lightweight, does not require a separate database
server, and is suitable for a local desktop application.

### IR6 — Development tooling

The project shall use Poetry for dependency management and shall include
`pytest`, `coverage`, `ruff`, and `mypy` for testing, coverage measurement,
linting, and static type checking.

**Justification:** These tools support reproducible development, code quality,
and automated validation.

## Glossary

| Term | Meaning |
|---|---|
| Meeting recording | An audio or video file containing a recorded meeting |
| Transcript | The textual representation of the speech contained in a meeting recording |
| Action item | An explicit task that someone should complete after the meeting |
| Owner | The person responsible for an action item, when explicitly stated in the transcript |
| Deadline | An explicit date by which an action item should be completed |
| Meeting summary | A concise description of the main discussion points, decisions, and follow-ups |
| Kanban board | A visual board that groups action items according to their status |
| Open action item | An action item that still needs to be completed |
| Done action item | An action item that has been marked as completed |
| Verified action item | A completed action item that has been reviewed and confirmed |
| Gemini | Google’s AI service used by MeetNote to analyze meeting transcripts |
| Whisper | A local speech-to-text model used to transcribe meeting recordings |

## Use-case diagram

The following diagram summarizes the main interactions between the user and
MeetNote. A meeting participant can create a meeting, upload a recording,
obtain its transcript, extract action items, generate a summary, and manage
tasks through the Kanban board. A meeting organizer focuses on reviewing and
managing the resulting action items and deadlines.

![Use-case diagram showing a meeting participant and meeting organizer interacting with MeetNote to create meetings, upload recordings, obtain transcripts, extract action items, generate summaries, and manage tasks and deadlines. The diagram presents these activities in a clear, functional software-workflow context.](../../pictures/requirements-use-case.svg)