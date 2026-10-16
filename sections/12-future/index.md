---
title: Future work
has_children: false
nav_order: 13
---

# Known issues and future work

## Known issues

The current version of MeetNote implements the main workflow for uploading a
meeting recording, transcribing it, extracting action items, generating a
meeting summary, and managing the results on a Kanban board. However, some
parts of the application are still limited.

One limitation is that MeetNote does not yet include user authentication. The
application is designed as a local desktop application, so it does not currently
support multiple user accounts, login, or role-based access control. Any person
using the computer can open the application and manage its locally stored data.

Another limitation is that the administrative review workflow is not fully
integrated into the Kanban board. The project defines a more detailed review
lifecycle with statuses such as `Submitted`, `Under review`, `Verified`,
`Needs changes`, and `Rejected`. However, the current Kanban board mainly uses
the simpler `Open`, `Done`, and `Verified` statuses.

The application also does not currently allow the user to edit an action item
after it has been extracted. A user can mark an item as done, verify it, reopen
it, or delete it, but cannot directly modify its description, owner, deadline,
or other details from the Kanban board.

The accuracy of action-item extraction depends on the quality of the meeting
recording and transcript. Background noise, unclear speech, multiple people
speaking at the same time, or ambiguous discussion can make it more difficult
for Whisper and Gemini to identify correct action items, owners, and deadlines.

Finally, the application currently focuses on one meeting at a time in its main
workflow. Although meetings and action items are stored in the local database,
the user interface does not yet provide a complete interface for browsing,
searching, and managing all previous meetings.

## Future work

Several improvements could make MeetNote more complete and useful.

### User accounts and roles

A future version could add user authentication and role-based access control.

This would allow different types of users to have different permissions. For
example, a regular user could view and update their assigned action items,
while an administrator could review generated items, edit them, verify them, or
reject them.

### Complete administrative review workflow

The administrative review model should be fully integrated into the user
interface.

This would allow an administrator to:

- Review candidate action items.
- Edit their description, owner, deadline, priority, and label.
- Approve and verify an action item.
- Request changes.
- Reject an incorrect action item.
- Add a review comment.

The existing status-transition rules in `meetnote/review.py` already define the
main lifecycle for this workflow.

### Action-item editing

The Kanban board should allow users to edit action items after they have been
created.

This would be useful when Gemini extracts a task correctly but identifies an
incomplete owner or deadline. The user could correct the information directly
from the board instead of deleting and recreating the action item.

### Better deadline management

The application could improve deadline management by adding:

- A calendar view.
- Notifications for approaching deadlines.
- Filters for overdue, upcoming, and completed tasks.
- Support for recurring tasks.
- More explicit handling of invalid or ambiguous dates.

### Improved AI analysis

The Gemini integration could be improved to extract more structured meeting
information, such as:

- Decisions.
- Risks.
- Open questions.
- Follow-up meetings.
- Meeting participants.
- Priority levels.
- References to the exact part of the transcript that supports each action item.

The application could also allow the user to review and confirm AI-generated
results before they are saved as action items.

### Multiple meetings and projects

A future version could provide a complete meeting-history view. This would
allow users to browse previous meetings, search transcripts, compare action
items, and see the overall progress of different projects.

The application could also group meetings and action items into projects, so
that users can manage several projects from the same interface.

### Cloud deployment

MeetNote could be deployed as a web application or as a cloud-hosted service.
This would allow team members to access the same meetings and action items from
different devices.

A cloud version would require user authentication, centralized data storage,
and more careful handling of meeting recordings and transcripts.

### Improved testing

Although the project already includes unit, integration, and persistence tests,
future work could add more automated system tests.

In particular, future tests could cover the complete user workflow, including
file selection, transcription, action-item extraction, summary generation, and
Kanban-board interactions.