# Clearcut tool-step record

A Clearcut (`play.googleapis.com/log`) record for one tool step. When the agent read `.git/logs/HEAD`, the record carried the file's contents, including the git author. Dotted numbers are protobuf field paths.

```text
POST play.googleapis.com/log
[…]
3.6.12.1.14.1: "file:///~/project/.git/logs/HEAD"
3.6.12.1.14.4:
  Created At: 2026-11-21T05:12:25Z
  Completed At: 2026-11-21T05:12:25Z
  Showing lines 1-1 of 1
  1: 0000000000000000000000000000000000000000 [commit ID] [git author] <[git email]> 1795237874 +0000	commit (initial): init
[…]
```
