---
title: Design
has_children: false
nav_order: 4
---

# Design

## Architecture

MeetNote follows a layered architecture. The application is divided into a user
interface, application services, domain models, external adapters, and
persistence components.

This structure was chosen because it separates the graphical interface from the
business logic and from external services. As a result, the way meetings are
displayed can change without changing how meetings are stored, and the Gemini
or Whisper components can be replaced without rewriting the whole application.

The high-level architecture is the following:

```text
+---------------------------+
|       User Interface       |
|         Kivy UI            |
+-------------+-------------+
              |
              v
+-------------+-------------+
|    Application Services    |
|      MeetingService        |
+------+------+------+------+
       |      |      |
       v      |      v
+------+------+---+---+------+
| Transcription | AI Analysis |
|   Whisper     |   Gemini    |
+---------------+-------------+
              |
              v
+---------------------------+
|        Persistence         |
|     SQLite repositories    |
+---------------------------+
```

### User-interface layer

The user-interface layer is implemented with Kivy. It contains two main
screens:

- The **Meeting Screen**, where the user creates a meeting, selects a recording,
  starts transcription, extracts action items, generates a summary, and opens
  the Kanban board.
- The **Kanban Screen**, where the user reviews action items, changes their
  status, deletes incorrect items, and checks upcoming deadlines.

The interface uses a background worker thread for long-running operations, such
as transcription, action-item extraction, and summary generation. This prevents
the graphical interface from freezing while the application waits for a
recording to be processed or for Gemini to respond.

### Application-service layer

The application-service layer contains `MeetingService`. This component
coordinates the main operations of the application:

- Creating a meeting.
- Transcribing a recording.
- Extracting action items.
- Generating a meeting summary.
- Listing, updating, reopening, verifying, and deleting action items.

The service acts as an intermediary between the user interface, the domain
model, external adapters, and the database repositories.

### Domain layer

The domain layer contains the main concepts of the application: `Meeting` and
`ActionItem`. These classes represent the information managed by MeetNote and
define the basic rules of the domain.

### External-adapter layer

The external-adapter layer contains the components that communicate with
external services or perform specialized processing:

- `LocalWhisperTranscriber`, which transcribes audio locally using
  `faster-whisper`.
- `FakeTranscriber`, which returns a predefined transcript for development and
  testing.
- `GeminiActionItemExtractor`, which communicates with the Gemini API.
- `FakeActionItemExtractor`, which returns predefined results for development
  and testing.

The application selects between the real and fake implementations using the
environment variables `MEETNOTE_TRANSCRIBER` and
`MEETNOTE_ACTION_EXTRACTOR`.

### Persistence layer

The persistence layer stores meetings and action items in a local SQLite
database. It contains two repositories:

- `MeetingRepository`, responsible for creating, reading, updating, and
  deleting meetings.
- `ActionItemRepository`, responsible for creating, reading, updating, and
  deleting action items.

## Infrastructure

MeetNote is a local desktop application, so it does not require a dedicated
application server, load balancer, message broker, or cloud database.

The application runs on the user’s machine. It stores its data in a local
SQLite database located in the `data` directory. The transcription component
also runs locally when Whisper is selected.

The only external service is the Google Gemini API. The application
communicates with it over HTTPS when the user selects the Gemini extractor. The
Gemini API key is read from the `GEMINI_API_KEY` environment variable.

```text
+---------------------+        HTTPS        +------------------+
|     MeetNote        | ------------------> |   Gemini API     |
| local Kivy desktop  |                     |  Google service  |
+---------------------+                     +------------------+
```

## Modelling

### Domain-driven design

The main bounded context of MeetNote is **meeting analysis and follow-up
management**. This context includes meetings, transcripts, action items,
owners, deadlines, and action-item statuses.

The main domain concepts are:

- **Meeting:** a recorded meeting with a title, optional notes, a transcript,
  and a creation timestamp.
- **Action item:** a concrete task identified from a meeting transcript.
- **Owner:** the person responsible for an action item, when explicitly stated.
- **Deadline:** the date by which an action item should be completed.
- **Status:** the current state of an action item.
- **Meeting summary:** a concise description of the main discussion points,
  decisions, and follow-ups.

The application uses repositories to separate domain objects from database
access. `MeetingRepository` manages `Meeting` objects, while
`ActionItemRepository` manages `ActionItem` objects.

### Object-oriented modelling

The main domain classes are `Meeting` and `ActionItem`.

A `Meeting` has the following attributes:

- `id`: unique meeting identifier.
- `title`: the name of the meeting.
- `notes`: optional notes associated with the meeting.
- `transcript`: the text produced by the transcription process.
- `created_at`: the creation timestamp.

An `ActionItem` has the following attributes:

- `id`: unique action-item identifier.
- `meeting_id`: identifier of the meeting that generated the item.
- `description`: the task to complete.
- `owner`: the responsible person, if known.
- `status`: the current state of the item.
- `due_date`: the deadline, if explicitly stated.

Each `ActionItem` belongs to exactly one `Meeting`. A meeting can have zero or
more action items. When a meeting is deleted, its action items are also deleted
because the database uses a cascading foreign-key relationship.

```text
Meeting 1 ---- 0..* ActionItem
```

### Review model

In addition to the simpler action-item lifecycle used by the Kanban board, the
project defines a more detailed administrative review model.

In this model, an action item can have the following statuses:

- `Submitted`
- `Under review`
- `Verified`
- `Needs changes`
- `Rejected`
- `Completed`

The allowed transitions are:

```text
Submitted -> Under review
Under review -> Verified
Under review -> Needs changes
Under review -> Rejected
Needs changes -> Under review
Verified -> Completed
```

This model represents the planned administrative review workflow. A candidate
action item is first submitted, then reviewed by an administrator. The
administrator can verify it, ask for changes, or reject it. A verified item can
later be marked as completed.

The application also defines labels for action items, such as `action_item`,
`decision`, `information`, `follow_up`, `deadline`, and `needs_clarification`.
It also defines priorities: `low`, `medium`, and `high`.

## Interaction

The main interaction flow is the following:

1. The user enters a meeting title and selects an audio or video file.
2. The user interface creates a meeting through `MeetingService`.
3. `MeetingService` sends the recording to the selected transcriber.
4. The transcriber produces a transcript.
5. `MeetingService` saves the transcript in the `MeetingRepository`.
6. The user requests action-item extraction.
7. `MeetingService` retrieves the meeting and sends its transcript to the
   selected action-item extractor.
8. Gemini returns structured action items.
9. `MeetingService` saves each action item in the `ActionItemRepository`.
10. The user opens the Kanban board to review and manage the extracted items.

The interaction with Gemini uses JSON responses. The application defines
Pydantic models for the expected response structure, which makes the parsing of
Gemini’s output more reliable.

```text
User -> Kivy UI -> MeetingService -> Transcriber -> SQLite
User -> Kivy UI -> MeetingService -> Gemini -> SQLite
```

## Behaviour

The Kanban board represents the state of action items using three visible
states:

```text
Open -> Done -> Verified
```

An action item is created with the `open` status. When the user marks it as
done, its status changes to `done`. A done item can then be verified. Both done
and verified items can be reopened, which returns them to the `open` status.

The application also defines a more detailed review lifecycle for future
administrative functionality:

```text
Submitted -> Under review -> Verified -> Completed
                        |
                        +-> Needs changes -> Under review
                        |
                        +-> Rejected
```

In this lifecycle, an item cannot move directly from `Submitted` to `Verified`.
It must first be placed under review. Likewise, a rejected item cannot be
changed further, and a completed item is final.

The user interface updates the Kanban board after every status change. It also
recalculates the number of open, done, and verified items and refreshes the
list of upcoming deadlines.

## Data-related aspects

MeetNote stores its data in a local SQLite database. SQLite was chosen because
it is lightweight, does not require a separate database server, and is suitable
for a desktop application used on a single machine.

The database contains two tables:

### `meetings`

| Column | Type | Description |
|---|---|---|
| `id` | Integer | Primary key |
| `title` | Text | Meeting title |
| `notes` | Text | Optional notes associated with the meeting |
| `transcript` | Text | Transcript generated from the recording |
| `created_at` | Text | Meeting creation timestamp |

### `action_items`

| Column | Type | Description |
|---|---|---|
| `id` | Integer | Primary key |
| `meeting_id` | Integer | Foreign key to the meeting |
| `description` | Text | Task to complete |
| `owner` | Text | Responsible person, if known |
| `due_date` | Text | Deadline, if explicitly stated |
| `status` | Text | Current status of the action item |

The `action_items.meeting_id` column refers to `meetings.id`. Foreign keys are
enabled, and deleting a meeting also deletes its action items.

The application performs the following main database operations:

- Insert a new meeting.
- Update a meeting transcript.
- Read a meeting by identifier.
- List meetings.
- Delete a meeting.
- Insert a new action item.
- List action items for a meeting.
- Update an action-item status.
- Delete an action item.

These operations are performed through repository classes, so the rest of the
application does not need to interact with SQL directly.