---
title: User guide
has_children: false
nav_order: 10
---

# User Guide

This section explains how to use MeetNote after it has been installed and
configured on your computer.

MeetNote is a desktop application with two main screens:

- The **Meeting Screen**, where you create a meeting, upload a recording,
  generate a transcript and summary, and extract action items.
- The **Kanban Screen**, where you manage the action items produced from the
  meeting.

## Starting MeetNote

After installing the dependencies, start the application with:

```powershell
py -3.12 -m poetry run python -m meetnote
```

On macOS or Linux, use:

```bash
poetry run python -m meetnote
```

The MeetNote window opens with the Meeting Screen.

## Creating a meeting

1. In the **Enter meeting title** field, type a title for your meeting.
2. Click **Select audio or video file**.
3. Choose an audio or video recording from your computer.
4. Click **Use selected file**.
5. Click **Submit meeting**.

MeetNote accepts the following file formats:

- Audio: `.mp3`, `.wav`, `.m4a`, `.flac`, `.ogg`
- Video: `.mp4`, `.mov`, `.avi`

After submitting the meeting, MeetNote starts the transcription process. The
status label shows the current state of the application.

## Viewing the transcript

When transcription is complete, the transcript appears in the **Transcript**
area of the Meeting Screen.

The status label also shows the length of the generated transcript. At this
point, the meeting has been saved and you can extract action items or generate
a meeting summary.

## Extracting action items

1. After the transcript is available, click **Extract action items**.
2. Wait while MeetNote analyzes the transcript.
3. When the process finishes, the action items appear in the **Action items**
   area.

MeetNote extracts only explicit and concrete tasks. If an action item has a
responsible person or deadline, this information is also shown.

If no action items are found, MeetNote displays:

```text
Status: no action items found
```

You can click **Refresh action items** at any time to reload the saved action
items for the current meeting.

## Generating a meeting summary

1. After the transcript is available, click **Generate meeting summary**.
2. Wait while MeetNote analyzes the transcript.
3. Read the generated summary in the **Meeting summary** area.

The summary includes the main discussion points, decisions, and follow-ups
found in the transcript.

## Opening the Kanban board

Click **Open Kanban board** to manage the action items of the current meeting.

The Kanban board contains three columns:

- **Open**: action items that still need to be completed.
- **Done**: action items that have been marked as completed.
- **Verified**: action items that have been reviewed and confirmed.

Each action-item card shows:

- The task description.
- The owner, or `All / unassigned` if no owner was identified.
- The deadline, or `No deadline` if no deadline was identified.

## Managing action items

The available buttons depend on the current status of an action item.

### Open action items

For an action item in the **Open** column, click:

- **Mark done** when the task has been completed.
- **Delete** if the action item is incorrect or no longer needed.

### Done action items

For an action item in the **Done** column, click:

- **Verify** to confirm that the task was completed correctly.
- **Reopen** if the task needs further work.
- **Delete** to remove the action item.

### Verified action items

For an action item in the **Verified** column, click:

- **Reopen** if the task needs to be worked on again.
- **Delete** to remove the action item.

After any change, the Kanban board refreshes automatically.

## Checking upcoming deadlines

Below the Kanban board, MeetNote shows upcoming deadlines for open action
items.

Each deadline entry shows:

- The due date.
- The action-item description.
- The owner, or `All / unassigned`.

The deadlines are sorted from the earliest to the latest. If no open action
item has a deadline, MeetNote shows:

```text
No upcoming deadlines
```

## Refreshing the board

Click **Refresh board** to reload the action items and update the Kanban board,
deadline list, and status counters.

The title of the Kanban board also shows how many action items are currently
open, done, and verified.

> Add screenshots of the Meeting Screen and Kanban Screen here. Before adding
> screenshots, make sure that no API keys, tokens, personal information, or
> private meeting content is visible.