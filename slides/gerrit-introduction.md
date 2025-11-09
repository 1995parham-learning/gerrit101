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

**Consistent Tooling**

- Single CI/CD pipeline
- Unified linting, testing, build tools

---

## Monorepo Advantages (continued)

**Simplified Dependency Management**

- All dependencies visible in one place
- No "diamond dependency" problems
- Easy to upgrade shared dependencies

**Better Collaboration**

- All teams see all code
- Knowledge sharing across teams
- Find experts and examples easily

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
vim services/api/handler.go
vim services/frontend/api-client.ts

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

**Monorepo Tools**

- Bazel (Google's build system)
- Pants (Twitter, Foursquare)
- Nx (JavaScript/TypeScript)

**Git Review CLI**

- `pip install git-review`

---

<!-- _class: invert lead -->

# Q&A

Questions?

---

<!-- _class: lead -->

# Let's Build Better Code Together

**With Gerrit + Monorepo**

Thank you!
