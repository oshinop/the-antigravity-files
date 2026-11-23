# A cron that should never fire

A `schedule` call with `0 0 31 2 *` (midnight on 31 February, a date that never occurs), captured with `agy` 1.5.14. The timer fired three times in 29 seconds, each tick waking the main agent for another model turn, until the task was cancelled with `manage_task`. IDs and the prompt text are replaced.

```text
--- Main agent: functionCall (prompt sent at 12:33:55) ---
schedule { "IsDaemon": false, "Prompt": "[text]", "CronExpression": "0 0 31 2 *" }

--- Ticks ---
<SYSTEM_MESSAGE>
[Message] timestamp=2026-11-22T12:34:11Z sender=[id]/task-28 priority=MESSAGE_PRIORITY_HIGH content=[text] (iteration 1)
</SYSTEM_MESSAGE>

<SYSTEM_MESSAGE>
[Message] timestamp=2026-11-22T12:34:28Z sender=[id]/task-28 priority=MESSAGE_PRIORITY_HIGH content=[text] (iteration 2)
</SYSTEM_MESSAGE>

--- manage_task kill: functionResponse.output, with the third tick appended ---
Created At: 2026-11-22T12:34:40Z
Completed At: 2026-11-22T12:34:41Z
Task "[id]/task-28" cancelled.

The following is a <SYSTEM_MESSAGE> not actually sent by the user. It is provided by the system as important information to pay attention to.

<SYSTEM_MESSAGE>
[Message] timestamp=2026-11-22T12:34:40Z sender=[id]/task-28 priority=MESSAGE_PRIORITY_HIGH content=[text] (iteration 3)
</SYSTEM_MESSAGE>
```
