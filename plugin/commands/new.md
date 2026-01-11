# ba New

Guided task creation with proper scoping and specification.

## Goal

Help users create well-specified, actionable ba tasks. Guide them from a rough idea to one or more properly scoped tasks.

## Step 1: Get initial input

Ask the user what they want to accomplish:

```text
What do you want to accomplish? Describe the task or goal in a few words.
```

If user provides something vague like "fix the thing" or "improve performance", probe for specifics:
- What specifically is broken or needs improvement?
- What behavior should change?
- What does success look like?

## Step 2: Assess scope

Evaluate if the task is properly scoped. A well-scoped task:
- Can be completed in a single focused session
- Has clear acceptance criteria
- Doesn't require breaking into subtasks

**Signs of oversized scope:**
- Multiple unrelated changes
- "And also..." in the description
- Would touch many different areas of code
- Contains multiple acceptance criteria that could stand alone

**If scope is too large:**

Suggest breaking down into smaller tasks:

```text
This sounds like it could be multiple tasks. I see:
1. [First discrete piece of work]
2. [Second discrete piece of work]
3. [Third discrete piece of work]

Should I create these as separate tasks? They can be linked as dependencies if needed.
```

## Step 3: Refine the title

Help craft an actionable title. Good titles:
- Start with a verb (Add, Fix, Update, Remove, Implement, Refactor)
- Are specific enough to understand without context
- Fit in ~10 words

**Transform vague titles:**
- "auth stuff" → "Add JWT token refresh on expiration"
- "fix bug" → "Fix login redirect loop when session expires"
- "improve perf" → "Add caching to database queries in search endpoint"

Propose a refined title and confirm:

```text
How about: "[refined title]"

Does this capture what you want to do?
```

## Step 4: Determine type

Based on the work described, recommend a type:

- **task** - Building something new or changing behavior
- **refactor** - Improving code without changing behavior
- **spike** - Research or investigation, time-boxed exploration
- **epic** - Container for related tasks (only if creating multiple)

```text
This sounds like a [type]. [Brief explanation of why]
```

## Step 5: Add context (optional)

For non-trivial tasks, ask if there's additional context:

```text
Any additional context to capture? For example:
- Acceptance criteria (what does "done" look like?)
- Technical constraints or approaches to consider
- Related files or areas of code
```

If user provides context, it becomes a comment on the created task.

## Step 6: Set priority

Offer priority options:

```text
Priority?
- 0: Critical - drop everything
- 1: High - do soon
- 2: Medium - normal queue (default)
- 3: Low - when you get to it
- 4: Backlog - someday/maybe
```

Default to P2 if user doesn't specify.

## Step 7: Handle dependencies (if multiple tasks)

If creating multiple tasks, ask about dependencies:

```text
Should any of these tasks block others?
For example: Task 2 depends on Task 1 being done first.
```

## Step 8: Create task(s)

Execute the creation:

```bash
# Single task
ba create "Title here" -t task -p 2

# With comment for context
ba create "Title here" -t task -p 2
ba comment <id> "Additional context here"

# Multiple tasks with dependencies
ba create "First task" -t task -p 2
ba create "Second task (depends on first)" -t task -p 2
ba block <second-id> <first-id>
```

## Step 9: Confirm

Show what was created:

```text
Created:
  <id>: Title (type, priority)

Ready to claim with:
  ba claim <id> --session $SESSION
```

Or for multiple tasks:

```text
Created 3 tasks:
  <id1>: First task (task, P2) - ready
  <id2>: Second task (task, P2) - blocked by <id1>
  <id3>: Third task (task, P2) - blocked by <id2>

Task tree:
  <id1> → <id2> → <id3>

Start with:
  ba claim <id1> --session $SESSION
```

## Quick mode

If user provides a clear, well-specified task upfront, skip the refinement:

```text
User: Create a task to add input validation to the signup form that rejects emails without @ symbol
```

This is already:
- Actionable (add validation)
- Specific (signup form, email field, @ symbol check)
- Properly scoped (single focused change)

Just confirm type and priority, then create.

## Examples

### Example 1: Vague input

```text
User: I need to fix auth
Claude: What specifically is broken with authentication?
        - Login not working?
        - Token expiration issues?
        - Permission checks failing?

User: Oh, the JWT tokens expire and users get logged out

Claude: Got it. How about: "Add JWT token refresh before expiration"
        This is a task (adding new behavior). Priority?

User: High, it's annoying users

Claude: Created:
  ab-x7k2: Add JWT token refresh before expiration (task, P1)

  Ready to claim: ba claim ab-x7k2 --session $SESSION
```

### Example 2: Oversized scope

```text
User: I want to add a dashboard

Claude: A dashboard could be quite broad. What should it show?

User: User stats, recent activity, and admin controls

Claude: This sounds like 3 separate pieces:
  1. "Add user statistics panel to dashboard"
  2. "Add recent activity feed to dashboard"
  3. "Add admin control panel to dashboard"

Should I create these as separate tasks? They can work independently.

User: Yes, and admin controls need the stats first

Claude: Created 3 tasks:
  ab-y1a2: Add user statistics panel to dashboard (task, P2) - ready
  ab-z3b4: Add recent activity feed to dashboard (task, P2) - ready
  ab-c5d6: Add admin control panel to dashboard (task, P2) - blocked by ab-y1a2

Start with either ab-y1a2 or ab-z3b4 (both ready).
```

### Example 3: Quick create

```text
User: Task to update the README with new installation instructions for ARM64

Claude: Clear and specific. Creating:
  "Update README with ARM64 installation instructions" (task, P2)

  ab-m7n8 created. Ready to claim.
```

---

Keep the conversation natural. Don't be overly formal or robotic. The goal is helping users think through their tasks, not running them through a rigid form.
