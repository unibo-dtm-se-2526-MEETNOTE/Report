---
title: Validation
has_children: false
nav_order: 6
---

# Validation

## Testing approach

The application was validated using automated tests and manual acceptance
testing. The automated tests focus on the domain model, transcription
components, Gemini integration, application services, and database persistence.

The project uses `pytest` as its testing framework. `pytest` was chosen because
it is simple to use, integrates well with Python projects, and allows test
functions to be written with clear names and assertions.

Test coverage is measured with `coverage`. The CI/CD workflow runs the test
suite, generates a coverage report, and uploads the HTML coverage report as a
GitHub Actions artifact.

The tests were executed using:

```bash
poetry run poe test
```

Coverage was measured using:

```bash
poetry run poe coverage
poetry run poe coverage-report
```

## Unit testing

The project includes 26 automated tests, and all of them pass successfully.
The overall test coverage is 93%. The tests cover the domain model, review
rules, transcription workflow, Gemini response parsing, API-key validation,
application services, and SQLite persistence.

### Domain-model tests

The tests in `tests/test_models.py` validate the main domain rules of the
application. In particular, they check that an action item must have a
non-empty title and a valid meeting identifier.

These tests are related to the correctness of the `ActionItem` domain model and
help prevent invalid data from being created.

### Transcription tests

The tests in `tests/test_transcription.py` and
`tests/test_meeting_transcription.py` validate the transcription components.

They verify that:

- A transcription service rejects a file path that does not exist.
- The transcription workflow produces a transcript.
- The transcript can be handled by the application service.

These tests are related to the requirement that MeetNote shall transcribe an
uploaded meeting recording.

### AI-extraction tests

The tests in `tests/test_ai.py` validate the Gemini action-item extractor and
the meeting-summary generation logic.

The Gemini extractor tests use a mocked Gemini client. This makes it possible
to test how the application parses a Gemini response without sending a real
request to the external service during every test run.

The tests verify that:

- The Gemini action-item extractor correctly parses action items.
- Extracted action items can include a description, owner, and due date.
- The application calls the Gemini client when extracting action items.
- The Gemini extractor can generate a meeting summary.
- The application raises a clear error when `GEMINI_API_KEY` is missing.

The following test verifies that the Gemini extractor requires an API key:

```python
test_gemini_extractor_requires_api_key
```

This test removes the `GEMINI_API_KEY` environment variable and checks that the
application raises the expected `ValueError`.

### Review-model tests

The tests in `tests/test_review.py` validate the administrative review
lifecycle defined in `meetnote/review.py`.

They check which status transitions are allowed and ensure that invalid
transitions raise an `InvalidStatusTransition` error. For example, an action
item must move from `Submitted` to `Under review` before it can be verified,
rejected, or marked as needing changes.

## Integration testing

Integration tests verify that multiple components work correctly together.

The main integration scenarios are:

- Creating a meeting through `MeetingService`.
- Extracting action items using the fake extractor.
- Saving the extracted action items in the SQLite database.
- Reading the saved action items again.
- Generating a meeting summary through the application service.

For example, `test_meeting_service_extracts_and_saves_action_items` creates a
meeting, extracts action items, and verifies that the resulting items are
stored with the correct description, owner, due date, and initial `open`
status.

Another integration test, `test_meeting_service_rejects_unknown_meeting`,
checks that the application behaves correctly when action-item extraction is
requested for a meeting that does not exist.

The tests use temporary SQLite databases created with pytest’s `tmp_path`
fixture. This ensures that each test runs in an isolated environment and does
not modify the developer’s local database.

## System testing

The project does not currently include a separate automated end-to-end system
test suite that starts the full Kivy graphical interface. However, the
application was tested manually as a complete system.

The main system-level scenarios were:

1. Start the MeetNote desktop application.
2. Create a meeting with a title.
3. Select an audio or video recording.
4. Submit the meeting and wait for transcription.
5. View the generated transcript.
6. Extract action items from the transcript.
7. Generate a meeting summary.
8. Open the Kanban board.
9. Mark an action item as done.
10. Verify a done action item.
11. Reopen an action item.
12. Delete an action item.
13. Check that upcoming deadlines are displayed correctly.

These scenarios correspond to the main functional requirements of the
application.

## Persistence testing

The tests in `tests/test_storage.py` validate the SQLite repositories and
database behaviour.

They verify that:

- An action item can be marked as done, reopened, and deleted.
- Foreign keys are enabled in the SQLite database.
- Deleting a meeting also deletes its associated action items.
- An action item cannot be associated with a non-existent meeting.
- Action items with deadlines can be sorted by due date.
- A done action item can be verified.
- The complete Kanban status flow works correctly.

The following status flow is covered by the tests:

```text
Open -> Done -> Verified -> Open
```

These tests are related to the requirements for action-item status management,
data persistence, upcoming deadlines, and deletion of action items.

## Acceptance tests

Manual acceptance tests were performed by using the application as an end user.

The following scenarios were tested:

1. Start MeetNote.
2. Enter a meeting title.
3. Select an audio or video file.
4. Submit the meeting.
5. Wait for the transcription result.
6. Extract action items.
7. Generate a meeting summary.
8. Open the Kanban board.
9. Mark an action item as done.
10. Verify the completed action item.
11. Reopen the action item.
12. Delete an action item.
13. Check that upcoming deadlines are shown for open action items.

The application was also tested without a Gemini API key to ensure that it
produces a clear error message instead of failing silently.

The automated test suite consists of 26 tests. All 26 tests passed successfully.

The tests were executed on Windows using Python 3.11.9 and pytest 8.4.2. The
complete test run completed in approximately five seconds.

The test suite achieved 93% overall code coverage. The coverage report shows
that the domain models, review rules, database setup, and storage tests are
fully covered. The application-service layer has 75% coverage, while the Gemini
integration has 96% coverage.

The lower coverage in `meetnote/application/transcription.py` is mainly due to
the local Whisper implementation, which requires the optional `faster-whisper`
dependency and processes real audio files. The uncovered lines in
`meetnote/application/services.py` are mainly related to meeting deletion,
manual action-item creation, and some error-handling paths.

Overall, the automated tests cover the main application behavior, including
Gemini response parsing, API-key validation, action-item extraction, meeting
summaries, action-item status transitions, database persistence, foreign-key
constraints, and upcoming-deadline sorting.