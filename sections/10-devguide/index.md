---
title: Self-evaluation
has_children: false
nav_order: 12
---

# Self-evaluation

## Sahand Atlasi

During this project, I developed MeetNote, a Python desktop application that
helps transform meeting recordings into reviewable and trackable action items.
The application allows a user to upload an audio or video recording, transcribe
it locally, extract explicit action items using Gemini, generate a meeting
summary, and manage the resulting tasks through a Kanban board.

My main role in the project was to design and implement the application
end to end. I worked on the Kivy user interface, the meeting and action-item
domain models, the application services, the SQLite persistence layer, the
Whisper transcription integration, and the Gemini action-item extraction and
summary-generation components.

I also configured the development and release workflow. In particular, I used
Git and GitHub to manage branches, commits, pull requests, and releases. I
configured GitHub Actions to run static checks, tests, and coverage analysis,
and I set up semantic-release to automate versioning, GitHub releases, and PyPI
publication.

One strength of the product is its clear separation between the user interface,
application logic, domain model, external adapters, and storage components.
This makes the application easier to maintain and allows the transcription and
AI components to be replaced or tested independently.

Another strength is the use of replaceable implementations for transcription
and action-item extraction. The application can use a fake transcriber and fake
extractor during development and testing, while also supporting local Whisper
transcription and Gemini-based analysis in real usage.

The Kanban board is also a useful part of the application. It gives a clear
visual representation of action items and their current state. Users can mark
items as done, verify them, reopen them, delete them, and check upcoming
deadlines.

A weakness of the current version is that the application does not yet include
user authentication or a complete role-based permission system. At the moment,
it is designed as a local desktop application, so any person using the machine
can access its data and manage action items.

Another limitation is that the administrative review workflow is not yet fully
integrated into the main Kanban interface. The project defines a more detailed
review lifecycle, with statuses such as `Submitted`, `Under review`,
`Needs changes`, and `Rejected`, but the current Kanban board mainly uses the
simpler `Open`, `Done`, and `Verified` statuses.

The quality of the AI-generated results also depends on the clarity of the
meeting transcript. MeetNote tries to extract only explicit action items, but
unclear audio, poor transcription quality, or ambiguous discussion can still
affect the accuracy of the extracted tasks and deadlines.

In future versions, I would like to improve user authentication, complete the
administrative review workflow, allow users to edit AI-generated action items
before saving them, and improve the overall accuracy of action-item extraction.
I would also like to add better support for multiple meetings and projects,
notifications for approaching deadlines, and a cloud deployment option.