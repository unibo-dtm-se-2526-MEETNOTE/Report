---
title: Developer guide
has_children: false
nav_order: 11
---

# Developer guide

This section explains how to set up a development environment for MeetNote,
how the code base is organized, and how to test, change, and release it.

## Prerequisites

- Python 3.11, 3.12, or 3.13
- [Poetry](https://python-poetry.org/) 2.x
- Git
- Optional: `ffmpeg`, if your system needs it to decode audio or video files for Whisper
- Optional: a Google Gemini API key, to use the real action-item extractor

## Setting up the project

Clone the repository and install the dependencies, including the development
tools:

```bash
git clone https://github.com/unibo-dtm-se-2526-MEETNOTE/Artifact.git
cd Artifact
poetry install
```

The virtual environment is created inside the project in `.venv`, as configured
in `poetry.toml`.

Local Whisper transcription is an optional Poetry dependency group. Install it
only if you need real transcription:

```bash
poetry install --with whisper
```

On Windows, prefix Poetry commands with the Python launcher, for example
`py -3.12 -m poetry install`.

## Project layout

```text
meetnote/
├── __main__.py                  # Entry point: python -m meetnote
├── ui/app.py                    # Kivy screens (MeetingScreen, KanbanScreen) and MeetNoteApp
├── application/
│   ├── services.py              # MeetingService: the use cases called by the UI
│   ├── transcription.py         # Transcriber protocol, FakeTranscriber, LocalWhisperTranscriber
│   ├── meeting_transcription.py # MeetingTranscriber helper around TranscriptionService
│   └── ai.py                    # ActionItemExtractor protocol, fake and Gemini extractors
├── domain/models.py             # Meeting and ActionItem used by the application (open/done/verified)
├── storage/
│   ├── database.py              # SQLite connection and schema
│   └── repositories.py          # MeetingRepository and ActionItemRepository
├── models.py                    # Review model: ActionItem with priority, label and review fields
└── review.py                    # Allowed status transitions for the review model
tests/                           # pytest test suite
.github/workflows/               # check.yml (CI) and deploy.yml (release)
```

Two `ActionItem` classes exist. `meetnote/domain/models.py` is the one used by
the running application and the Kanban board. `meetnote/models.py` together
with `meetnote/review.py` defines the planned administrative review workflow,
which is not yet connected to the user interface.

## How the layers fit together

- The **UI** (`ui/app.py`) only calls `MeetingService`. It never runs SQL or
  calls Gemini directly.
- **`MeetingService`** coordinates the use cases: creating meetings,
  transcribing recordings, extracting action items, generating summaries, and
  changing action-item status.
- **Transcribers and extractors** are defined as `typing.Protocol`s. Any class
  with a `transcribe(audio_path)` method can be used as a transcriber; any class
  with `extract_action_items(meeting)` and `generate_meeting_summary(meeting)`
  can be used as an extractor.
- **Repositories** are the only place that contains SQL.

The concrete implementations are chosen once, in
`MeetNoteApp._create_meeting_service`, based on environment variables.

### Adding a new transcriber or extractor

1. Implement a class with the methods required by the `Transcriber` or
   `ActionItemExtractor` protocol.
2. Add a branch for it in `MeetNoteApp._create_meeting_service`, selected
   through `MEETNOTE_TRANSCRIBER` or `MEETNOTE_ACTION_EXTRACTOR`.
3. Add tests that pass the new class to `MeetingService` with a temporary
   database.

### Changing the database

The schema is defined in `storage/database.py` and is created with
`CREATE TABLE IF NOT EXISTS` every time a connection is opened. There is no
migration system: adding a column to an existing table does not update
databases that already exist. During development, delete `data/meetnote.db`
after a schema change.

## Configuration

| Variable | Values | Default | Effect |
|---|---|---|---|
| `MEETNOTE_TRANSCRIBER` | `fake`, `whisper` | `fake` | `whisper` uses `LocalWhisperTranscriber` (base model, CPU, int8). `fake` returns a fixed demo transcript. |
| `MEETNOTE_ACTION_EXTRACTOR` | `fake`, `gemini` | `fake` | `gemini` uses `GeminiActionItemExtractor`; `fake` returns two demo action items. |
| `GEMINI_API_KEY` | API key | none | Required when the Gemini extractor is selected. |

The variables are read from the process environment. Set them in the shell
before starting the application. Never commit API keys to the repository.

The SQLite database is stored in `data/meetnote.db`, relative to the directory
from which the application is started.

## Running the application

```bash
poetry run python -m meetnote
```

## Tests and quality checks

All tasks are defined with Poe the Poet in `pyproject.toml`:

| Command | Purpose |
|---|---|
| `poetry run poe test` | Run the test suite with pytest |
| `poetry run poe coverage` | Run the tests while measuring coverage |
| `poetry run poe coverage-report` | Print the coverage report |
| `poetry run poe coverage-html` | Generate the HTML report in `htmlcov/` |
| `poetry run poe static-checks` | Run Ruff linting and mypy |
| `poetry run poe format` | Format the code with Ruff |
| `poetry run poe format-check` | Check formatting without changing files |
| `poetry run poe compile` | Check that all modules compile |

Run `static-checks`, `format-check` and `test` before opening a pull request;
the CI workflow runs the same commands.

### Writing tests

- Use pytest's `tmp_path` fixture for a temporary SQLite database, so tests
  never touch `data/meetnote.db`.
- Use `FakeTranscriber` and `FakeActionItemExtractor` to test services without
  audio files or network access.
- To test the Gemini extractor, pass a mocked client
  (`unittest.mock.MagicMock`) to `GeminiActionItemExtractor(client=...)`, as in
  `tests/test_ai.py`. No real API call or key is needed.

## Branches, commits and pull requests

- Create a branch from `master`, named `feature/<short-description>` or
  `fix/<short-description>`.
- Write commit messages in the Conventional Commits format
  (`feat:`, `fix:`, `test:`, `refactor:`, `docs:`, `chore:`, `ci:`). The
  message type decides the next version number.
- Open a pull request into `master`. The CI workflow must pass before merging.

## Releasing

Releases are automatic. When a commit reaches `master` and CI passes,
`deploy.yml` runs semantic-release, which:

1. Determines the next version from the commit messages.
2. Updates the version in `pyproject.toml` and the changelog.
3. Builds and publishes the package to PyPI.
4. Creates the Git tag and the GitHub release.

Do not change the version number or create tags by hand. The `PYPI_TOKEN`
repository secret must contain a valid PyPI token for publication to work.
See the [Release](../06-release/) and [CI/CD](../08-cicd/) sections for details.

## Working on this report

The report is a Jekyll site using the Just the Docs theme. To preview it
locally:

```bash
bundle install
bundle exec jekyll serve
```

Each section lives in `sections/<nn-name>/index.md`; its position in the menu
is set by `nav_order` in the front matter. Diagrams are stored in `pictures/`.
