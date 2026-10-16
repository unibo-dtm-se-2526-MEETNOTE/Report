---
title: Development
has_children: false
nav_order: 5
---

# Development

## DVCS

The project was developed using Git and GitHub. The source code is stored in
the `Artifact` repository of the `unibo-dtm-se-2526-MEETNOTE` GitHub
organization.

The `master` branch contains the main development line. New functionality and
bug fixes were developed in separate branches and integrated into `master`
through pull requests.

The project used the following branch naming convention:

- `feature/<short-description>` for new functionality.
- `fix/<short-description>` for bug fixes.

Examples of branches used during development include:

- `feature/gemini-meeting-summary`
- `feature/gemini-action-item-due-dates`
- `fix/gemini-api-key-validation`

Commit messages followed the Conventional Commits style. This means that
commits usually began with a type, followed by a short description of the
change. The main types used were:

- `feat`: a new feature.
- `fix`: a bug fix.
- `test`: a test-related change.
- `refactor`: a change that improves the code without changing its behavior.
- `chore`: maintenance or configuration work.
- `ci`: continuous-integration or deployment changes.

Examples of commit messages include:

- `feat: add due date to Gemini action item extraction`
- `feat: generate meeting summaries with Gemini`
- `fix: validate Gemini API key before extractor initialization`
- `test: cover complete Kanban action item status flow`
- `refactor: share meeting service between screens`

Changes were committed at meaningful checkpoints instead of waiting until the
end of the development process. This made it easier to understand the history
of the project and to identify when each feature or fix was introduced.

Pull requests were used to integrate feature and fix branches into `master`.
For example, pull request #4 integrated Gemini-based meeting-summary
generation, while pull request #5 integrated the validation of the Gemini API
key before extractor initialization.

The repository also uses automated dependency updates through Renovate. This
helps keep dependencies up to date and reduces the risk of using outdated
libraries.

## Implementation details

MeetNote is a local desktop application, so it does not use a client-server
network protocol for its main functionality. The graphical interface, local
database, and transcription component run on the user’s machine.

The application communicates with the Google Gemini API over HTTPS. HTTPS is
used because it encrypts the communication between MeetNote and the external AI
service.

Data sent to and received from Gemini is represented in JSON format. The
application uses Pydantic models to define the expected structure of Gemini’s
response. In particular, action-item extraction expects a JSON object
containing a list of items, where each item has a description and optional
owner and due-date fields.

The application stores meetings and action items in a local SQLite database.
SQL queries are used through Python’s built-in `sqlite3` library. This approach
was chosen because SQLite does not require a separate database server and is
suitable for a local desktop application.

The Gemini API key is provided through the `GEMINI_API_KEY` environment
variable. It is not hard-coded in the source code. The application validates
that the key is present before initializing the Gemini extractor.

The application does not currently implement user authentication or
role-based authorization. It is designed as a local desktop application, so the
user of the machine has access to the application and its local data.

## Technological details

The project was implemented in Python. The project supports Python versions
from 3.11 to 3.13, as defined in `pyproject.toml`.

Kivy was used to create the desktop graphical user interface. Kivy provides the
components needed to build the meeting screen, file-selection dialog, Kanban
board, buttons, labels, and pop-ups.

The main dependencies used by the project are:

| Dependency | Purpose |
|---|---|
| Kivy | Desktop graphical user interface |
| google-genai | Integration with the Gemini API |
| faster-whisper | Optional local transcription of audio recordings |
| Pydantic | Validation and parsing of structured Gemini responses |
| SQLite | Local persistence of meetings and action items |
| Poetry | Dependency and packaging management |
| pytest | Automated testing |
| coverage | Test-coverage measurement |
| Ruff | Code formatting and linting |
| mypy | Static type checking |
| Renovate | Automated dependency updates |

Whisper is an optional dependency. When it is selected through the
`MEETNOTE_TRANSCRIBER` environment variable, MeetNote uses
`LocalWhisperTranscriber` to transcribe the meeting recording locally. If
Whisper is not selected, the application uses `FakeTranscriber`, which returns
a predefined transcript for development and testing.

Similarly, MeetNote can use either `GeminiActionItemExtractor` or
`FakeActionItemExtractor`. The Gemini extractor is selected through the
`MEETNOTE_ACTION_EXTRACTOR` environment variable. This design makes the
application easier to test and allows the core functionality to work without
requiring an external AI service.

## External dependencies

The main external service used by MeetNote is the Google Gemini API. It is used
to extract explicit action items from a meeting transcript and to generate a
concise meeting summary.

The application sends the meeting title, optional meeting notes, and transcript
to Gemini. For action-item extraction, it asks Gemini to return only explicit
and concrete tasks, without inventing additional work. For meeting summaries,
it asks Gemini to include the main discussion points, decisions, and follow-ups
without adding information that is not present in the