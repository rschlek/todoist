# Quick capture from an iPhone

An optional capture front end: one press of the iPhone **Action Button** (or a
Home Screen or Lock Screen shortcut) drops a raw capture into the Todoist Inbox
without opening the app. `todoist:triage` turns those captures into well-formed
items later. No shortcut file ships with this plugin; building one takes a
minute and keeps your account details out of it.

## Build it

1. Install the Todoist app and sign in. The shortcut drives the app's own
   Shortcuts actions, so no API token is involved.
2. In the **Shortcuts** app, create a new shortcut and add Todoist's add-task
   action (named "Create Task" or "Add Task" depending on the app version).
3. Set the project to **Inbox** and the **title to a fixed placeholder**,
   `New Task`. Set the **description** to *Ask Each Time* (or an "Ask for Input"
   action ahead of it), so the text you type lands in the description.
4. Name it (for example "Create Task").
5. In iOS **Settings > Action Button**, choose **Shortcut** and pick it.

Why the placeholder title: the prompt then asks for one free-text field and the
capture stays fast. Triage recognizes the placeholder (default conventions A3),
promotes the description into a proper verb-first title, and clears the
description. If you pick a different placeholder, name it in your own
conventions file so triage recognizes it.

## Where it fits

Captures arrive raw - placeholder title, text in the description, no section,
date, or priority - which is exactly the start state triage expects. The capture
stays dumb and fast; triage is where each item is classified and brought to the
standard.
