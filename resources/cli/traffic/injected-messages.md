# Injected messages

Text the client adds to the conversation of a main agent or swarm member, from `agy` 1.5.14 model requests. Each block is one text part as sent; IDs are replaced.

```text
--- First user entry of a conversation (one text part) ---
<USER_REQUEST>
Have the research subagent explore this project, then summarize what it does in two sentences.
</USER_REQUEST>
<ADDITIONAL_METADATA>
The current local time is: 2026-11-22T14:20:48Z.
</ADDITIONAL_METADATA>
<USER_SETTINGS_CHANGE>
The user changed setting `Model Selection` from None to Gemini 4 Neon (Medium). No need to comment on this change if the user doesn't ask about it. If reporting what model you are, please use a human readable name instead of the exact string.
</USER_SETTINGS_CHANGE>

--- A slash command: /swarm puts the whole skill inside ADDITIONAL_METADATA ---
<ADDITIONAL_METADATA>
The current local time is: 2026-11-22T13:10:41Z.

The user has mentioned some items in the form @[ITEM]. Here is extra information about the items that were mentioned by the user, in the order that they appear:

/swarm is a [Slash Command]:
<SKILL>The user has explicitly invoked the (swarm) skill. You must strictly follow the instructions in this skill to process their request. Here are its contents:
# You are the swarm coordinator
[…]</SKILL>
</ADDITIONAL_METADATA>

--- A helper's report, appended to the output of the main agent's next tool result ---
Created At: 2026-11-22T14:21:53Z
Completed At: 2026-11-22T14:21:54Z
Command exited with code 127.
[…]

The following is a <SYSTEM_MESSAGE> not actually sent by the user. It is provided by the system as important information to pay attention to.

<SYSTEM_MESSAGE>
[Message] timestamp=2026-11-22T14:21:44Z sender=[helper conversation ID] priority=MESSAGE_PRIORITY_HIGH content=## Project Summary (`~/project`)

**Purpose:**
A minimal Python **Billing service** ("Small invoice calculator") that computes invoice totals including tax.
[…]
</SYSTEM_MESSAGE>

--- The batching reminder, appended to the output of an edit_file result ---
Made 1 replacement in ~/project/src/billing.py starting at line 5 (+5/-0 lines).

The following is a <SYSTEM_MESSAGE> not actually sent by the user. It is provided by the system as important information to pay attention to.

<SYSTEM_MESSAGE>
Reminder: work efficiently. In this turn, issue in parallel every independent search, read and edit or write you need next. Never one call per turn; never repeat a search or re-read content you already know that hasn't changed. Working efficiently never means skipping checks to save turns; verify in one command after your last edit and don't re-run passing tests or diffs without new edits.
</SYSTEM_MESSAGE>

--- A timer tick from the schedule tool (its own user entry) ---
The following is a <SYSTEM_MESSAGE> not actually sent by the user. It is provided by the system as important information to pay attention to.

<SYSTEM_MESSAGE>
[Message] timestamp=2026-11-22T13:13:10Z sender=[conversation ID]/task-6 priority=MESSAGE_PRIORITY_HIGH content=[monitor] tick: check the swarm for a stall (iteration 1)
</SYSTEM_MESSAGE>

--- A swarm board notification, seen by a member ---
The following is a <SYSTEM_MESSAGE> not actually sent by the user. It is provided by the system as important information to pay attention to.

<SYSTEM_MESSAGE>
[Message] timestamp=2026-11-22T13:16:11Z sender=[conversation ID] priority=MESSAGE_PRIORITY_HIGH content=[swarm] new board message #5
</SYSTEM_MESSAGE>
```
