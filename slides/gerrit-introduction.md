---
marp: true
theme: custom
paginate: true
header: "Introduction to Gerrit"
---

<!-- _class: invert lead -->

# Introduction to Gerrit

**Code Review at Scale**

From GitHub to Gerrit

---

## Why Gerrit?

- **Scales better** for large teams & repos
- **Enforces strict** code review before merge
- **Integrates** with CI/CD pipelines
- **Fine-grained** access control & approvals

---

## GitHub vs Gerrit (High-Level)

| Feature           | GitHub PRs              | Gerrit Reviews                      |
| ----------------- | ----------------------- | ----------------------------------- |
| **Review Model**  | Pull Request            | Patch-based, per-commit review      |
| **Merge Control** | Branch protection       | Mandatory approval + CI checks      |
| **Workflow**      | Fork & PR               | Push for review (refs/for/\*)       |
| **Scale**         | Medium → Large projects | Very Large projects (Android, etc.) |

---

## Gerrit Workflow Basics

1. Clone repo
2. Create change locally
3. Push to `refs/for/<branch>`
4. Review happens in Gerrit UI
5. Approvals (Code-Review + Verified)
6. Merge to target branch

---

## Gerrit UI Overview

- **Change screen** - commit details, diffs
- **Review labels** - Code-Review, Verified, +2 required
- **Inline comments** on diffs
- **Multiple patchsets** per change
- **Powerful search** and filtering

---

<!-- _class: invert -->

# Squash vs Gerrit Approach

**Different Philosophies**

---

## GitHub: Squash and Merge

**Common GitHub workflow:**

```bash
# Multiple commits on feature branch
git commit -m "Start feature"
git commit -m "WIP"
git commit -m "Fix bug"
git commit -m "Address review comments"
git commit -m "Fix typo"

# On merge: All commits → 1 squashed commit
```

**Result:** Clean main branch, but **loses granular history**

---

## Gerrit: One Commit, One Change

**Gerrit philosophy:**

```bash
# One logical change = One commit
git commit -m "Add user authentication feature"

# Review feedback? Amend the same commit
git commit --amend

# Push again - same Change-Id, new patchset
git push origin HEAD:refs/for/main
```

**Result:** Clean history **from the start**, every commit reviewable

---

## Why Gerrit's Approach Wins

**Squash Merge Problems:**

- Can't review individual commits
- Loses development history
- Hard to find when bugs were introduced
- Can't cherry-pick specific changes easily

---

## Why Gerrit's Approach Wins (cont.)

**Gerrit Benefits:**

- **Every commit is reviewed** before merge
- **Clean history by design**, not by accident
- `git bisect` works perfectly
- Easy cherry-picking for backports

<blockquote class="callout-tip">
<strong>Bottom line:</strong> Quality enforced at commit time, not hidden at merge time
</blockquote>

---

## Squash vs Gerrit: The Verdict

| Aspect                  | Squash (GitHub)                  | Gerrit                |
| ----------------------- | -------------------------------- | --------------------- |
| **History cleanliness** | Clean main, messy feature branch | Always clean          |
| **Granularity**         | Lost on squash                   | Preserved             |
| **Review**              | Review branch, merge 1 commit    | Review each commit    |
| **Debugging**           | Hard to bisect                   | Easy to bisect        |
| **Cherry-pick**         | All or nothing                   | Pick specific commits |

<blockquote class="callout-note">
<strong>Gerrit forces good habits</strong>, squash tries to hide bad ones
</blockquote>

---

<!-- _class: invert -->

# Understanding Gerrit Labels

**The Review System Explained**

---

## Code-Review Label

The **Code-Review** label is the primary approval mechanism:

| Score  | Meaning                    | Description                 |
| ------ | -------------------------- | --------------------------- |
| **+2** | Looks good to me, approved | Final approval - can submit |
| **+1** | Looks good, but not final  | Supportive feedback         |
| **0**  | No score                   | Just commenting             |
| **-1** | I would prefer you didn't  | Minor concerns to address   |
| **-2** | Do not submit              | Hard veto - blocks merge    |

<blockquote class="callout-info">
<strong>Rule:</strong> Need at least one <strong>+2</strong> and <strong>no -2</strong> votes to merge
</blockquote>

---

## Verified Label

The **Verified** label confirms build/test status:

| Score  | Meaning               | Who                        |
| ------ | --------------------- | -------------------------- |
| **+1** | Verified - tests pass | CI/CD or manual verify     |
| **0**  | No verification yet   | -                          |
| **-1** | Fails verification    | Build broken or tests fail |

**Usually automated** by CI systems (Jenkins, GitLab CI, etc.)

<blockquote class="callout-info">
<strong>Rule:</strong> Need at least one <strong>+1</strong> and <strong>no -1</strong> to merge
</blockquote>

---

## Label Workflow Example

```
Change submitted → status:open
  ↓
CI runs tests → Verified: +1 (if pass) or -1 (if fail)
  ↓
Reviewer 1 → Code-Review: +1 (looks ok)
  ↓
Reviewer 2 → Code-Review: +2 (approved!)
  ↓
Submit button enabled → Change merged
```

<blockquote class="callout-warning">
<strong>Both labels must be satisfied to submit</strong>
</blockquote>

---

## Gerrit Change Screen

![width:900px](images/gerrit-change-screen.png)

Key elements: files changed, commit message, reviewers, labels, and comments

---

## Review Interface

![width:500px](images/gerrit-review.png)

Add comments, vote on labels, and provide feedback

---

## Patchsets: Multiple Revisions

**Patchset** = a revision of your change

- First upload → Patchset 1
- Fix review feedback, amend commit, push again → Patchset 2
- Keep updating → Patchset 3, 4, etc.

**Same Change-Id** keeps them linked together

![width:700px](images/gerrit-patchsets.png)

---

## Who Can Vote?

**Code-Review +2/-2:**

- Usually **project maintainers** or **core reviewers**
- Configured per project/branch

**Code-Review +1/-1:**

- Any registered user with access
- Contributors, team members

---

## Who Can Vote? (cont.)

**Verified +1/-1:**

- Usually **CI/CD systems** (automated)
- Can be manual for some projects

**Permissions are configurable** in project settings

**Note:** Access control is managed through:

- Project-level permissions
- Branch-level rules
- Group membership

---

## OWNERS Files

**Fine-grained code ownership in monorepos**

OWNERS files define who can approve changes in specific directories:

```
# /services/auth/OWNERS
# People who can approve auth service changes
alice@company.com
bob@company.com

# Or use groups
auth-team@company.com
```

**Benefits:**

- Ensures domain experts review their areas
- Scales review process across large codebases

---

## OWNERS File Features

**Hierarchical ownership:**

```
/                    # Root OWNERS
  global-leads@company.com

/services/api/       # API team owns this
  api-team@company.com

/services/api/auth/  # Auth specialists override
  security-team@company.com
```

---

## OWNERS File Features (cont.)

<blockquote class="callout-tip">
<strong>Gerrit integration:</strong> Automatically adds reviewers based on changed files
</blockquote>

**Common in:** Chromium, Android, Kubernetes, and large monorepos

**Advanced features:**

- Wildcards for file patterns
- `set noparent` to stop inheritance
- Per-file OWNERS rules

---

## Review Best Practices

**Voting Guidelines:**

- Use **+1** for "looks good, minor suggestions"
- Use **+2** only when you're **confident** and authorized
- Use **-1** for concerns that should be addressed
- Use **-2** sparingly - only for serious issues
- Always **explain your vote** in comments

---

## Review Best Practices (cont.)

**Review Focus:**

- Code quality and readability
- Architecture and design
- Tests and documentation
- Security and performance

**Communication:**

- Be respectful and constructive
- Ask questions, don't make assumptions
- Acknowledge good work

---

## Developer Life: Before Gerrit

```
git commit -m "fix"
git commit -m "fix fix"
git commit -m "actually fix"
git commit -m "why doesn't this work"
git commit -m "FINALLY WORKING"
git push --force
```

_Sound familiar?_ 😅

---

## Advantages in Practice

- Review **every commit**, not just the branch
- Rebase workflow encourages **clean history**
- Easier **cherry-picking / backporting**
- Standard in many **enterprise/open-source** orgs

---

<!-- _class: invert -->

# Monorepo Benefits

**Why We Should Adopt a Monorepo Strategy**

---

## What is a Monorepo?

> A single repository containing multiple projects, services, or components

**Examples:**

- Google (billions of lines of code)
- Facebook/Meta
- Microsoft
- Uber

---

## Monorepo + Gerrit = Power Combo

<div style="display: flex; gap: 2rem;">
<div style="flex: 1;">

### Monorepo Benefits

- Atomic changes
- Shared code
- Unified versioning
- Single source of truth

</div>
<div style="flex: 1;">

### Gerrit Benefits

- Per-commit review
- Dependency tracking
- Cross-project changes
- Enforced standards

</div>
</div>

---

## Key Monorepo Advantages

**Code Reuse & Sharing**

- Share libraries, utilities, configs across projects
- No dependency hell or versioning conflicts

**Atomic Cross-Project Changes**

- Refactor APIs across multiple services in one commit
- Gerrit reviews the entire change atomically

---

## Key Monorepo Advantages (cont.)

**Consistent Tooling**

- Single CI/CD pipeline
- Unified linting, testing, build tools

**Easy Refactoring**

- IDE can refactor across all projects
- Breaking changes detected immediately

---

## More Monorepo Benefits

**Simplified Dependency Management**

- All dependencies visible in one place
- No "diamond dependency" problems
- Easy to upgrade shared dependencies

---

## More Monorepo Benefits (cont.)

**Better Collaboration**

- All teams see all code
- Knowledge sharing across teams
- Find experts and examples easily

**Easier Onboarding**

- One repo to clone
- Complete codebase visibility

---

## Migration Impact

- Less **'PR discussions'**, more commit-focused reviews
- Developers **push to Gerrit** instead of opening PRs
- **CI/CD integration** enforces automated checks
- Teams need to adapt to **Gerrit's review labels**

---

## Gerrit + Monorepo Workflow

```bash
# Clone the monorepo
git clone https://gerrit.company.com/monorepo
cd monorepo

# Make changes across multiple services
nvim services/api/handler.go
nvim services/frontend/api-client.ts

# Commit with Change-Id
git commit -m "Update API endpoint across services"

# Push for review
git push origin HEAD:refs/for/main
```

---

## When to Use Gerrit

- Projects with **many contributors**
- Projects requiring **strict review policy**
- Cases where **clean commit history** is critical
- **Monorepo** environments with cross-cutting changes

---

<!-- _class: invert -->

# Gerrit Tips & Hints

Best Practices for Success

---

## Essential Gerrit Tips

- Always push changes to `refs/for/<branch>` (not directly to the branch)
- Use `git commit --amend` to update last commit before re-pushing
- **One commit = one logical change** → keeps history clean

<blockquote class="callout-warning">
<strong>Important:</strong> Make sure every commit has a <strong>Change-Id</strong> (use commit-msg hook)
</blockquote>

---

## More Tips

- Install **'git review'** tool for simpler workflow
- Use Gerrit labels (Code-Review, Verified) instead of 'LGTM' comments
- **Rebase often**: `git fetch && git rebase origin/main`
- Squash trivial fixups into original commit before re-pushing

---

## Setting Up Gerrit Hooks

```bash
# Install the commit-msg hook
curl -Lo .git/hooks/commit-msg \
  https://gerrit.company.com/tools/hooks/commit-msg
chmod +x .git/hooks/commit-msg

# Now every commit gets a Change-Id automatically
git commit -m "Add new feature"
# Change-Id: I1234567890abcdef...
```

---

## The Code Review Cycle

```
Developer: "This is my best code ever!"
         ↓
Reviewer: "Have you considered...?"
         ↓
Developer: "Oh no, you're right"
         ↓
*Amends commit 47 times*
         ↓
Reviewer: "LGTM! +2"
         ↓
CI: "Build failed"
         ↓
Developer: *cries*
```

---

## Code Review Best Practices

**For Authors:**

- Keep commits small and focused
- Write clear commit messages
- Respond to feedback promptly
- Don't take criticism personally

---

## Code Review Best Practices (cont.)

**For Reviewers:**

- Review promptly (within 24 hours)
- Be constructive and specific
- Focus on code, not the person
- Use +1, +2 labels appropriately

---

## Comparison: Multi-Repo vs Monorepo

| Aspect                    | Multi-Repo                        | Monorepo                           |
| ------------------------- | --------------------------------- | ---------------------------------- |
| **Cross-project changes** | Multiple PRs, coordination needed | Single atomic commit               |
| **Code sharing**          | NPM/packages, versioning issues   | Direct imports, no versions        |
| **CI/CD**                 | Per-repo pipelines                | Unified pipeline                   |
| **Permissions**           | Per-repo                          | Fine-grained in monorepo           |
| **Complexity**            | Many repos to manage              | Single repo, better tooling needed |

---

## Challenges & Solutions

**Challenge:** Large repo size
**Solution:** Partial clones, sparse checkout

**Challenge:** Long CI times
**Solution:** Incremental builds, affected tests only

**Challenge:** Learning curve
**Solution:** Training, documentation, git-review tool

---

## Partial Clone for Large Monorepos

**Problem:** Monorepo is huge, clone takes forever

**Solution:** Git partial clone

```bash
# Clone without blobs initially
git clone --filter=blob:none \
  https://gerrit.company.com/monorepo
```

**How it works:**

- Downloads commits & trees
- Skips blobs (file contents)
- Fetches on-demand when needed

---

## Partial Clone: Filter Options

**Different filter types:**

```bash
# No blobs (fetch on demand)
git clone --filter=blob:none <url>

# Skip large blobs (>1MB)
git clone --filter=blob:limit=1m <url>

# Advanced: treeless clone
git clone --filter=tree:0 <url>
```

---

## Partial Clone: Real Impact

**Performance comparison:**

| Metric          | Full Clone | Partial Clone |
| --------------- | ---------- | ------------- |
| **Size**        | 10 GB      | 500 MB        |
| **Time**        | 45 minutes | 3 minutes     |
| **Fetch blobs** | Upfront    | On-demand     |

**Blobs downloaded automatically when accessed**

---

## Partial Clone + Sparse Checkout

**Combine for maximum efficiency:**

```bash
# Step 1: Partial clone
git clone --filter=blob:none \
  https://gerrit.company.com/monorepo
cd monorepo

# Step 2: Sparse checkout
git sparse-checkout init --cone
git sparse-checkout set services/auth services/api
```

**Result:**

- Fast clone (no blobs upfront)
- Small working dir (specific services only)

---

## Partial Clone: When to Use

**Good candidates:**

- Monorepos over 5 GB
- Teams on isolated services
- CI builds (partial repo)

**Best for:**

- Large monorepos with microservices
- Developers on specific components
- Build pipelines

---

## Partial Clone: Best Practices

**Tips:**

- Use `--filter=blob:none` by default
- Combine with sparse checkout
- CI/CD: clone only what you need

**Limitations:**

- Some operations trigger fetches
- Requires Git 2.22+ (2019)
- Server must support it (Gerrit ✓)

---

<!-- _class: invert -->

# Gerrit SSH Interface

**Command-Line Power**

---

## SSH Setup

**Add your SSH key to Gerrit:**

1. Generate SSH key (if needed): `ssh-keygen -t ed25519`
2. Go to Gerrit UI → Settings → SSH Keys
3. Add your public key (`~/.ssh/id_ed25519.pub`)

![width:600px](images/gerrit-ssh-setup.png)

---

## SSH Setup (cont.)

**Configure SSH (~/.ssh/config):**

```bash
Host gerrit
  HostName gerrit.company.com
  Port 29418
  User your-username
```

**Test connection:**

```bash
ssh -p 29418 username@gerrit.company.com
ssh gerrit  # if using config
```

---

## Gerrit SSH Commands Overview

```bash
# Get list of available commands
ssh gerrit gerrit --help

# Stream events in real-time
ssh gerrit gerrit stream-events

# Query changes
ssh gerrit gerrit query [OPTIONS] QUERY

# Review changes
ssh gerrit gerrit review [OPTIONS] CHANGE,PATCHSET
```

---

## Querying Changes

**Basic queries:**

```bash
# Your pending changes
ssh gerrit gerrit query status:open owner:self

# Changes waiting for your review
ssh gerrit gerrit query status:open reviewer:self
```

---

## Querying Changes (cont.)

**Project and topic queries:**

```bash
# Changes in a project
ssh gerrit gerrit query status:open project:myproject

# Changes with topic
ssh gerrit gerrit query topic:feature-auth
```

---

## Querying Changes: Advanced

**Format and filtering:**

```bash
# JSON output
ssh gerrit gerrit query --format=JSON change:12345

# Recent changes
ssh gerrit gerrit query --format=TEXT status:open limit:5
```

---

## Querying Changes: Combining Filters

**Complex queries:**

```bash
# By age
ssh gerrit gerrit query status:open age:2d

# Multiple filters
ssh gerrit gerrit query status:open \
  project:myproject owner:self limit:10
```

---

## Query Operators

**Status Filters:**

- `status:open` - open changes
- `status:merged` - merged changes
- `status:abandoned` - abandoned changes

**User Filters:**

- `owner:USERNAME` - changes by user
- `reviewer:USERNAME` - changes being reviewed

---

## Query Operators (cont.)

**Project and Branch Filters:**

- `project:PROJECT_NAME` - changes in project
- `branch:BRANCH_NAME` - changes on branch
- `topic:TOPIC` - changes with topic

**Time Filters:**

- `age:DAYS` - older than N days
- Example: `age:2d`, `age:7d`

---

## Query Operators (cont.)

**Special Filters:**

- `is:watched` - watched changes
- `is:starred` - starred changes
- `label:Code-Review+2` - specific label
- `has:draft` - with draft comments

**Combine filters:**

```bash
ssh gerrit gerrit query status:open owner:self age:7d
```

---

## Reviewing via SSH

**Basic voting:**

```bash
# Vote on a change (+1 code review)
ssh gerrit gerrit review 12345,1 --code-review +1

# Vote +2 and verify
ssh gerrit gerrit review 12345,2 \
  --code-review +1

# Add a message with your review
ssh gerrit gerrit review 12345,3 \
  --code-review -1 \
  --message "Please fix the formatting issues"
```

---

## Reviewing via SSH (cont.)

**Submit and abandon:**

```bash
# Submit a change (merge it)
ssh gerrit gerrit review 12345,4 --submit

# Abandon a change
ssh gerrit gerrit review 12345,1 \
  --abandon \
  --message "No longer needed"

# Restore an abandoned change
ssh gerrit gerrit review 12345,1 --restore
```

---

## Stream Events (Real-time Monitoring)

**Monitor Gerrit activity in real-time:**

```bash
# Stream all events
ssh gerrit gerrit stream-events

# Filter events with jq
ssh gerrit gerrit stream-events | \
  jq 'select(.type == "patchset-created")'

# Monitor specific project
ssh gerrit gerrit stream-events | \
  jq 'select(.change.project == "myproject")'
```

**Event types:** `patchset-created`, `comment-added`, `change-merged`, `change-abandoned`, `reviewer-added`

---

## Practical SSH Examples

**Daily workflow queries:**

```bash
# What needs my review?
ssh gerrit gerrit query status:open reviewer:self limit:10

# What did I submit recently?
ssh gerrit gerrit query owner:self status:merged age:7d
```

---

## Practical SSH Examples (cont.)

**Status and submission queries:**

```bash
# Check CI status of a change
ssh gerrit gerrit query change:12345 --current-patch-set

# Find changes ready to submit
ssh gerrit gerrit query status:open label:Code-Review+2
```

---

## SSH vs Web UI

| Task                     | SSH                 | Web UI           |
| ------------------------ | ------------------- | ---------------- |
| **Quick queries**        | ✓ Fast, scriptable  | Manual search    |
| **Batch operations**     | ✓ Easy with scripts | Tedious          |
| **Code review**          | Limited             | ✓ Better UI      |
| **CI/CD integration**    | ✓ Perfect           | API needed       |
| **Real-time monitoring** | ✓ stream-events     | Requires polling |

**Best practice:** Use SSH for automation, queries, and monitoring; use Web UI for detailed code review.

---

<!-- _class: invert -->

# Dependent Changes

**Building on Top of Unmerged Work**

---

## The Scenario: Dependent Features

**Common situation:**

- You're working on Feature B
- But Feature B needs Feature A
- Feature A is still under review (not merged yet)

**In GitHub:** You'd create a new branch from your feature branch

**In Gerrit:** You create a **change chain** (dependent changes)

---

## Creating Dependent Changes

**Step 1: Create first change**

```bash
# On main branch
git checkout main
git pull

# Create Feature A
nvim feature-a.go
git add .
git commit -m "Add Feature A: User authentication"
git push origin HEAD:refs/for/main
```

**Change 12345 created** ✓

---

## Creating Dependent Changes (cont.)

**Step 2: Create dependent change**

```bash
# DON'T go back to main!
# Stay on current branch with Feature A

# Create Feature B (builds on Feature A)
nvim feature-b.go
git add .
git commit -m "Add Feature B: User authorization (depends on Feature A)"
git push origin HEAD:refs/for/main
```

**Change 12346 created** ✓ (depends on 12345)

---

## How Gerrit Tracks Dependencies

**Gerrit automatically detects the dependency:**

- Change 12346 has Change 12345 as its parent commit
- In Gerrit UI, you'll see "Related Changes" section
- Shows the dependency chain clearly

**Visualization:**

```
main branch
    ↓
Change 12345 (Feature A) - Under Review
    ↓
Change 12346 (Feature B) - Under Review
```

---

## Reviewing Dependent Changes

**Reviewers can see the full context:**

- Each change can be reviewed independently
- Or review the entire chain together
- Gerrit shows which changes are blocking others

---

## Reviewing Dependent Changes (Cont'd)

**Review order matters:**

- Feature A must be approved first
- Feature B can be reviewed in parallel
- But can't be merged until Feature A merges

<blockquote class="callout-tip">
<strong>Pro tip:</strong> Use topics to group related changes: <code>git push origin HEAD:refs/for/main%topic=user-auth-system</code>
</blockquote>

---

## Updating Dependent Changes

**If Feature A needs changes after review:**

```bash
# Go back to Feature A commit
git rebase -i HEAD~2  # Interactive rebase

# Or use git checkout to the specific commit
git log --oneline  # Find commit hash
git checkout abc123  # Feature A commit
```

**Make changes:**

```bash
nvim feature-a.go
git add .
git commit --amend
git push origin HEAD:refs/for/main
```

---

## Updating Dependent Changes (cont.)

**Now rebase Feature B on top of updated Feature A:**

```bash
# Checkout Feature B commit
git checkout HEAD@{1}  # Or use commit hash

# Rebase onto the amended Feature A
git rebase abc456  # New hash of Feature A
git push origin HEAD:refs/for/main
```

**Both changes updated** ✓

<blockquote class="callout-warning">
<strong>Important:</strong> Always rebase dependent changes after updating parent changes
</blockquote>

---

## The Easier Way: Stay on Branch

**Simpler workflow for dependent changes:**

```bash
# Create Feature A
git commit -m "Add Feature A"
git push origin HEAD:refs/for/main

# Stay on same branch, create Feature B
git commit -m "Add Feature B (depends on A)"
git push origin HEAD:refs/for/main

# Both changes pushed, dependency tracked automatically
```

**To update Feature A:**

```bash
git rebase -i HEAD~2  # Edit the first commit
# Make changes, amend
git rebase --continue
git push origin HEAD:refs/for/main  # Updates both changes
```

---

## Merging Dependent Changes

**Gerrit enforces the correct order:**

1. Feature A gets approved (+2, Verified +1)
2. Submit Feature A → merges to main
3. Feature B automatically rebases onto main
4. Feature B can now be submitted

**Gerrit does this automatically!**

<blockquote class="callout-info">
<strong>Cherry-pick on merge:</strong> When Feature A merges, Gerrit can auto-rebase Feature B onto the new main
</blockquote>

---

## Dependent Changes: Best Practices

**Keep chains short:**

- 2-3 changes is ideal
- Long chains are harder to review and manage

**Make each change self-contained:**

- Each commit should be reviewable on its own
- Clear commit messages explaining dependencies

**Use topics for grouping:**

- Helps reviewers understand the bigger picture
- `git push origin HEAD:refs/for/main%topic=feature-name`

---

## Dependent Changes: Best Practices (cont.)

**Communicate with reviewers:**

- Add comments explaining the dependency
- Link related changes in commit messages
- Example: "Depends-On: Change-Id I1234567..."

**Test each change:**

- CI should run on each change independently
- But consider testing the full chain too

---

## Common Pitfall: Lost Dependencies

**What NOT to do:**

```bash
# Wrong: Going back to main breaks the chain
git checkout main
git commit -m "Feature B"  # No longer depends on Feature A!
git push origin HEAD:refs/for/main
```

**Right approach:**

```bash
# Correct: Stay on branch or explicitly rebase
git commit -m "Feature B"  # Still has Feature A as parent
git push origin HEAD:refs/for/main
```

---

## Real-World Example

**Scenario:** API change across services

```bash
# Change 1: Update API contract
git commit -m "Update User API: add email field"
git push origin HEAD:refs/for/main

# Change 2: Update API implementation (depends on Change 1)
git commit -m "Implement email field in User service"
git push origin HEAD:refs/for/main

# Change 3: Update API client (depends on Change 2)
git commit -m "Add email field to frontend user client"
git push origin HEAD:refs/for/main
```

**Result:** 3-change chain, each reviewable, merges in order

---

## Dependent Changes vs Feature Branches

| Aspect                 | Feature Branch (GitHub) | Change Chain (Gerrit)     |
| ---------------------- | ----------------------- | ------------------------- |
| **Dependency**         | Branch-level            | Commit-level              |
| **Review granularity** | Entire branch           | Each commit individually  |
| **Merge order**        | Manual coordination     | Enforced by Gerrit        |
| **Rebase complexity**  | Rebase entire branch    | Rebase individual commits |
| **Atomicity**          | All or nothing          | Incremental merging       |
| **Cherry-pick**        | Difficult               | Easy (individual commits) |

<blockquote class="callout-note">
<strong>Gerrit's approach:</strong> More granular, more flexible, cleaner history
</blockquote>

---

<!-- _class: invert -->

# Advanced Gerrit Workflows

**Deep Dive into Real-World Usage**

---

## Topics and Hashtags

**Topics group related changes:**

```bash
# Push with topic
git push origin HEAD:refs/for/main%topic=user-auth-refactor

# Query by topic
ssh gerrit gerrit query topic:user-auth-refactor

# Set topic on existing change
ssh gerrit gerrit set-topic 12345 user-auth-refactor
```

---

**Hashtags for categorization:**

```bash
# Add hashtag via review
ssh gerrit gerrit review 12345,1 --tag security-fix

# Query by hashtag
ssh gerrit gerrit query hashtag:security-fix
```

---

## Rebasing and Merge Conflicts

**Rebase your change onto latest main:**

```bash
# Fetch latest
git fetch origin

# Rebase onto main
git rebase origin/main

# Push updated change
git push origin HEAD:refs/for/main
```

---

**If conflicts occur:**

```bash
# Resolve conflicts in files
nvim conflicted-file.go

git add conflicted-file.go
git rebase --continue
git push origin HEAD:refs/for/main
```

---

## Rebasing and Merge Conflicts (cont.)

**Aborting a bad rebase:**

```bash
git rebase --abort
```

---

**Download change from Gerrit to rebase locally:**

```bash
# From change page, copy checkout command
git fetch origin refs/changes/45/12345/2
git checkout FETCH_HEAD
git rebase origin/main
git push origin HEAD:refs/for/main
```

<blockquote class="callout-warning">
<strong>Important:</strong> Always rebase before submitting to avoid merge conflicts
</blockquote>

---

## Abandoning and Restoring Changes

**Abandon a change you no longer need:**

```bash
# Via SSH
ssh gerrit gerrit review 12345,1 \
  --abandon \
  --message "Superseded by change 12346"

# Via git-review
git review -A "No longer needed"
```

**Restore an abandoned change:**

```bash
ssh gerrit gerrit review 12345,1 \
  --restore \
  --message "Actually still needed"
```

---

## Reverting Changes

**Revert a merged change:**

**Via Gerrit UI:**

1. Open merged change
2. Click "Revert" button
3. Gerrit creates revert change automatically

**Via command line:**

```bash
git revert abc123  # Commit hash
git push origin HEAD:refs/for/main
```

**Creates new change that undoes the original**

---

## Inline Comments and Discussions

**Best practices for inline comments:**

**For reviewers:**

- Be specific about location
- Quote relevant code
- Suggest concrete improvements
- Mark as resolved when addressed

**For authors:**

- Reply to all comments
- Mark "Done" when fixed
- Push new patchset with fixes
- Use "Reply" to continue discussion

---

## Inline Comments and Discussions (cont.)

**Gerrit tracks comment threads:**

- Comments stay visible across patchsets
- Can see what changed between revisions
- "Resolved" comments are hidden by default

**Draft comments:**

- Write comments without publishing
- Review all comments before sending
- Send all at once with overall vote

---

## Handling Large Changes

**When your change is too big:**

**Option 1: Split into smaller changes**

```bash
# Use interactive rebase to split commits
git rebase -i HEAD~3
# Mark commits to 'edit'
# Split and create multiple commits
# Push each as separate change
```

**Option 2: Use topics to group**

```bash
# Push related changes with same topic
git push origin HEAD:refs/for/main%topic=big-refactor
```

---

## Handling Large Changes (cont.)

**Best practices:**

- Keep changes under 500 lines when possible
- One logical change per commit
- Split refactoring from feature work
- Use dependent changes for phased rollout

<blockquote class="callout-note">
<strong>Rule of thumb:</strong> If you can't review it in 30 minutes, it's too big
</blockquote>

---

## Automated Workflows with Gerrit

**CI/CD integration patterns:**

**Jenkins Integration:**

```groovy
// Jenkinsfile
pipeline {
    triggers {
        gerrit customUrl: '',
               gerritProjects: [[pattern: 'myproject', branches: ['main']]],
               triggerOnPatchsetCreated: true
    }
    stages {
        stage('Test') {
            steps {
                sh 'make test'
            }
        }
    }
    post {
        success {
            gerritReview labels: [Verified: 1]
        }
        failure {
            gerritReview labels: [Verified: -1]
        }
    }
}
```

---

## Troubleshooting Common Issues

**Issue: Missing Change-Id**

```bash
# Install commit-msg hook
curl -Lo .git/hooks/commit-msg \
  https://gerrit.example.com/tools/hooks/commit-msg
chmod +x .git/hooks/commit-msg

# Add Change-Id to existing commit
git commit --amend
# Hook adds Change-Id automatically
```

---

## Troubleshooting Common Issues (cont.)

**Issue: "Cannot merge due to path conflict"**

```bash
# Fetch latest main
git fetch origin main

# Rebase your change
git rebase origin/main

# Resolve conflicts, then continue
git add resolved-file.go
git rebase --continue

# Push updated change
git push origin HEAD:refs/for/main
```

---

## Troubleshooting Common Issues (cont.)

**Issue: "You are not allowed to upload merges"**

```bash
# You accidentally created a merge commit
git log --oneline --graph  # Check for merge

# Fix: Rebase to create linear history
git rebase origin/main
git push origin HEAD:refs/for/main
```

**Prevention:** Always rebase, don't merge

---

## Troubleshooting Common Issues (cont.)

**Issue: "No new changes"**

**Cause:** Pushing same commit again

**Fix:**

```bash
# Make a change
nvim some-file.go
git add .
git commit --amend  # Amend, don't create new commit
git push origin HEAD:refs/for/main
```

**Remember:** Same Change-Id = same change, different patchset

---

## Performance Tips for Large Repos

**Shallow clones:**

```bash
# Clone with limited history
git clone --depth=1 https://gerrit.example.com/huge-repo
```

**Sparse checkout:**

```bash
# Only checkout specific directories
git sparse-checkout init
git sparse-checkout set services/my-service
```

**Partial clone:**

```bash
# Clone without blobs initially
git clone --filter=blob:none https://gerrit.example.com/repo
```

---

## Advanced Query Techniques

**Complex query combinations:**

```bash
# Open changes older than 7 days without +2
ssh gerrit gerrit query \
  -- 'status:open age:7d -label:Code-Review+2'

# Changes I authored that need rebasing
ssh gerrit gerrit query \
  -- 'owner:self status:open conflicts:any'

# Changes with many comments (probably controversial)
ssh gerrit gerrit query \
  -- 'status:open commentcount:>10'
```

---

## Advanced Query Techniques (cont.)

**Saved searches in Gerrit UI:**

Create dashboard with common queries:

- Your pending changes
- Changes needing your review
- Changes you starred
- Changes in your team's projects

**Access via:** Settings → Preferences → My Menu

---

## Hooks and Automation

**Git hooks for Gerrit workflows:**

**pre-commit hook:**

```bash
#!/bin/bash
# .git/hooks/pre-commit
# Run linters before commit
npm run lint || exit 1
go fmt ./... || exit 1
```

---

**prepare-commit-msg hook:**

```bash
#!/bin/bash
# Add issue tracker reference
BRANCH=$(git rev-parse --abbrev-ref HEAD)
ISSUE=$(echo $BRANCH | grep -oE '[A-Z]+-[0-9]+')
echo "Issue: $ISSUE" >> $1
```

---

<!-- _class: invert lead -->

# Summary

---

## Key Takeaways

**GitHub** → Simple PR workflow
**Gerrit** → Strict, scalable review system

**Great for:**

- Enterprise & large-scale OSS
- Teams adopting monorepo strategy
- Projects requiring audit trails

**Key mindset shift:**
Review every change, not just merges

---

## Resources

**Gerrit Documentation**

- [gerrit-review.googlesource.com](https://gerrit-review.googlesource.com)

**Git Review CLI**

- `pip install git-review`

---

## Resources (cont.)

**Monorepo Tools**

- Bazel (Google's build system)
- Pants (Twitter, Foursquare)
- Nx (JavaScript/TypeScript)

**Community**

- Gerrit User Summit
- #gerrit on IRC/Slack

---

## The Truth About Code Review

> What developers say:
> "I love code reviews! They make my code better."

> What developers think:
> "Please approve quickly so I can go to lunch"

> What actually happens:
> "Why are you changing my entire architecture
> in the comments? It's just a typo fix!"

**But seriously... code reviews are awesome!** 👍

---

<!-- _class: invert lead -->

# Q&A

Questions?

---

<!-- _class: lead -->

# Let's Build Better Code Together

**With Gerrit + Monorepo**

Thank you!
