---
marp: true
theme: custom
paginate: true
header: "Introduction to Gerrit"
footer: "Code Review at Scale"
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

**Rule:** Need at least one **+2** and **no -2** votes to merge

---

## Verified Label

The **Verified** label confirms build/test status:

| Score  | Meaning               | Who                        |
| ------ | --------------------- | -------------------------- |
| **+1** | Verified - tests pass | CI/CD or manual verify     |
| **0**  | No verification yet   | -                          |
| **-1** | Fails verification    | Build broken or tests fail |

**Usually automated** by CI systems (Jenkins, GitLab CI, etc.)

**Rule:** Need at least one **+1** and **no -1** to merge

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

**Both labels must be satisfied to submit**

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
- Make sure every commit has a **Change-Id** (use commit-msg hook)
- **One commit = one logical change** → keeps history clean

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

```bash
# Your pending changes
ssh gerrit gerrit query status:open owner:self

# Changes waiting for your review
ssh gerrit gerrit query status:open reviewer:self

# Changes in a specific project
ssh gerrit gerrit query status:open project:myproject

# Changes with specific topic
ssh gerrit gerrit query topic:feature-auth
```

---

## Querying Changes (cont.)

```bash
# Get detailed JSON output
ssh gerrit gerrit query --format=JSON change:12345

# Recent changes with commit messages
ssh gerrit gerrit query --format=TEXT status:open limit:5

# Changes by age
ssh gerrit gerrit query status:open age:2d

# Combine multiple filters
ssh gerrit gerrit query status:open project:myproject \
  owner:self limit:10
```

---

## Query Operators

**Status and User Filters:**

- `status:open` / `status:merged` / `status:abandoned`
- `owner:USERNAME` - changes by specific user
- `reviewer:USERNAME` - changes being reviewed by user

**Project and Branch Filters:**

- `project:PROJECT_NAME` - changes in specific project
- `branch:BRANCH_NAME` - changes targeting a branch
- `topic:TOPIC` - changes with specific topic

---

## Query Operators (cont.)

**Time and Special Filters:**

- `age:DAYS` - changes older than N days (e.g., `age:2d`)
- `is:watched` / `is:starred` - your watched/starred changes
- `label:Code-Review+2` - changes with specific label
- `has:draft` - changes with draft comments

**Combine with AND:**

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
  --code-review +2 \
  --verified +1

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
# What needs my review today?
ssh gerrit gerrit query status:open reviewer:self limit:10

# What did I submit recently?
ssh gerrit gerrit query owner:self status:merged age:7d

# Check CI status of a change
ssh gerrit gerrit query change:12345 --current-patch-set

# Find changes waiting for submit
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

<!-- _class: invert lead -->

# Q&A

Questions?

---

<!-- _class: lead -->

# Let's Build Better Code Together

**With Gerrit + Monorepo**

Thank you!
