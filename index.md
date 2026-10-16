---
title: MeetNote
layout: home
has_children: false
nav_order: 1
---

# MeetNote

### Author

- [Sahand Atlasi](mailto:sahand.atlasi@studio.unibo.it)

## Abstract

MeetNote is a Python desktop application that helps administrators transform
recorded meetings into reviewable and trackable action items. Users can upload
an audio or video recording of a meeting, and the application transcribes it
locally using Whisper.

After the transcript is available, MeetNote can use the Gemini API to extract
explicit action items and generate a concise meeting summary. The application
identifies concrete tasks, responsible people when explicitly mentioned, and
deadlines when they are stated in the transcript.

The extracted action items are managed through a Kanban board. Users can mark
items as done, verify completed work, reopen items that need further work, and
delete incorrect entries. The board also shows upcoming deadlines for open
action items.

MeetNote was developed using Python, Kivy, SQLite, Whisper, and the Gemini API.
The project uses Git and GitHub for version control, pull requests, automated
checks, and release management. GitHub Actions automates testing, coverage
analysis, semantic versioning, GitHub releases, and publication to PyPI.

## Disclaimer

During the preparation of this work, the author used AI-based tools to support
writing, debugging, and documentation. After using these tools, the author
reviewed and edited the content as needed and takes full responsibility for the
content of the final report and artifact.