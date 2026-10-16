---
title: Concept
has_children: false
nav_order: 2
---

# Concept

MeetNote is a Python desktop application that helps administrators transform
recorded meetings into reviewable and trackable action items. Instead of
requiring users to write detailed notes during a meeting, MeetNote allows them
to upload an audio or video recording and process it after the meeting has
finished.

The application follows a simple workflow. First, the user creates a meeting by
entering a title and selecting an audio or video file. The recording is then
transcribed locally using Whisper. After the transcript is available, MeetNote
can analyze it using the Gemini API to identify candidate action items and
generate a concise meeting summary.

The main purpose of MeetNote is to make meeting outcomes easier to manage. The
application extracts only explicit and concrete tasks from the transcript, so
that users can see what was actually agreed during the meeting. When available,
it also identifies the person responsible for an action item and any explicit
deadline.

MeetNote is intended for administrators or meeting organizers who need to
review the results of a meeting and assign follow-up work. It can be useful for
student project teams, small organizations, or any group that records meetings
and wants