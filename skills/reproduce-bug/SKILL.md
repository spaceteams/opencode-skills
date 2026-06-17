---
name: reproduce-bug
description: Reproduce a bug from a jira ticket and fix it.
---

### 1. Fetch the issue

Use /skill:jira-issue-context to fetch the given issue number, read it and read the `CONTEXT.md` in the project root to gain understanding of key domain terms.

### 2. Reproduce the bug

Run the dev command to start up a local instance. Use chrome devtools mcp to reproduce the bug locally. Understand what the root cause of the bug is. If you need additional information ask questions to the user. Once reproduced create tests verifying the bug. The test needs to check the expected behavior and fail.

### 3. Propose a fix

Once root cause is identified and at least one test covers the broken code path, present that test and propose the changes necessary to fix the bug.

### 4. Implement fix

After the user approves use /skill:tdd to implement the fix.

### 5. Verify fix

Check the implementation end to end with chrome devtools mcp by verifying that the bug is fixed and none additional breaking behavior has been introduced.

Run the full check pipeline to ensure compliance.