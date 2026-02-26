# Contributing to Nanobot AOS Documentation

Thanks for wanting to help — contributions of all sizes are welcome here, from fixing a typo to writing a full workflow guide.

Not sure if your idea is a good fit? Open an issue and ask. We're happy to talk it through before you spend time writing.

## Ways to Contribute

### 1. **Report Documentation Issues**
Found a broken link, outdated information, or unclear explanation? That's a valid contribution.

- **Open an issue** with:
  - Which guide has the problem
  - What's wrong (link broken, info outdated, unclear)
  - Suggested fix if you have one
  - Your environment (OS, Python version, Nanobot version)

### 2. **Suggest Improvements**
Have ideas for new content or better explanations?

- **Open an issue** with:
  - Title: "Documentation improvement: [topic]"
  - Description of what would help
  - Your use case
  - Suggested content outline (if substantial)

Examples of valuable improvements:
- Additional workflow examples solving real problems
- Platform-specific guidance (new channels, deployment patterns)
- Troubleshooting guides for common issues
- Cost analysis for specific use cases
- Video tutorial links or transcripts
- Translations to other languages

### 3. **Submit a Pull Request**
Ready to contribute code changes directly?

#### For Small Fixes (broken links, typos, grammar)
1. Fork this repository
2. Create a branch: `git checkout -b fix/issue-description`
3. Make your changes
4. Commit with a clear message: `git commit -m "Fix: broken link in AOS Startup Simple Build"`
5. Push: `git push origin fix/issue-description`
6. Open a pull request with:
   - What you fixed
   - Why it matters
   - Any testing you did

#### For New or Significantly Updated Guides
1. **Check if a guide already covers this** — search [Master Index](docs/Master-Index.md) first
2. **Propose first** — open an issue to discuss:
   - Topic and scope
   - Why it's needed
   - Target audience (beginners, operators, developers, teams)
3. **Use the template** — Start with [Procedure-Template.md](templates/Procedure-Template.md)
4. **Follow the style** — Match existing guides:
   - Clear, numbered steps for procedures
   - Code examples tested against Nanobot v0.1.4+
   - Security warnings flagged with ⚠️
   - Cross-references to related guides
   - JSON/Python/YAML examples match working configurations
5. **Include examples** — Workflows should be completed end-to-end, reproducible examples
6. **Link everything** — Reference related guides in "See Also" section
7. **Request review** — Someone from the team will review within 5 business days

#### For Workflow Examples
Workflow examples are especially valuable! When adding one:
- Start with the [Workflow Examples guide](docs/Workflow-Examples-and-Recipes.md) for format
- Test the workflow in a real environment (local setup)
- Include:
  - One clear business problem it solves
  - Prerequisites (skills, channels, providers needed)
  - Step-by-step setup with sample configuration
  - Expected output with example
  - Estimated monthly cost
  - Variations (how to adapt for different scenarios)
- Document any channel-specific notes (Slack vs Telegram setup differences, etc.)

## Style Guide

### Writing Style
- **Clear and direct** — Use active voice; avoid jargon where possible
- **Practical** — Assume the reader wants to build/operate something
- **Consistent** — Match the style of existing docs
- **Structured** — Use numbered steps for procedures, bullets for lists

### Formatting
- **Headings:** Use `#` for main title, `##` for sections, `###` for subsections (max depth: ###)
- **Code blocks:** Always specify language (```python, ```json, ```bash)
- **Links:** Use relative paths within the docs: `[Master Index](Master-Index.md)`
- **Warnings:** Use ⚠️ emoji for security/risk warnings
- **Tables:** Use markdown tables for comparisons and matrices
- **Bold:** Use **bold** for important terms and action items
- **Examples:** Always include working, tested examples

### Markdown Template
```markdown
# Guide Title

**Purpose:** One-line description of what this guide helps you do.

## 1. Overview (Optional)
Context and why this matters.

## 2. Prerequisites
What you need before starting.

## 3. Step-by-Step Procedure
Numbered steps with examples.

## 4. Verification
How to confirm it worked.

## 5. Troubleshooting
Common issues and solutions.

## 6. See Also
Links to related guides.
```

### Code Example Standards
- All code examples must be tested against Nanobot v0.1.4+
- Include output/results for educational clarity
- Comment complex configurations
- Show both working examples and common mistakes (with explanation)

**Python example:**
```python
# Configure multi-provider routing (Tier A = premium, B = balanced, C = budget)
config = {
    "llm": {
        "routing": {
            "rules": [
                {"channel": "#prd-*",      "tier": "a", "provider": "anthropic",   "model": "claude-opus"},
                {"channel": "#research-*", "tier": "b", "provider": "openrouter",  "model": "qwen2:72b"},
                {"channel": "#bk-*",       "tier": "c", "provider": "openrouter",  "model": "deepseek-67b"},
                {"keyword":  "urgent",     "tier": "a", "provider": "anthropic",   "model": "claude-opus"}
            ]
        }
    }
}
```

**JSON example:**
```json
{
  "channels": {
    "#prd-marketing": {
      "provider": "openrouter",
      "tier": "b",
      "monthly_budget": 50
    }
  }
}
```

## Review Process

1. **Submit PR** — Describe what changed and why
2. **Automated checks** — Links validated, markdown formatted correctly
3. **Human review** — We check for:
   - Accuracy (tested against actual code)
   - Clarity (well-explained for the target audience)
   - Completeness (examples work, links valid)
   - Style consistency (matches the other guides)
4. **Feedback or approval** — Comments on the PR if changes are needed
5. **Merge** — Once approved, your contribution is live!

**Timeline:** Most reviews are done within 3–5 business days. Typo fixes and broken links get priority.

## Running Documentation Locally

Want to preview your changes before submitting?

```bash
# Install MkDocs and Material theme
pip install mkdocs mkdocs-material

# Serve docs locally
mkdocs serve

# Visit http://localhost:8000
```

Then edit markdown files in `/docs` — changes update live in the browser.

To build static HTML (like GitHub Pages):
```bash
mkdocs build
# Output in /site folder
```

## Questions?

- **Not sure if your idea fits?** Open an issue and ask — no commitment required.
- **Something in a guide confusing you?** That's useful feedback. Open an issue describing where you got stuck.
- **Want to collaborate on something bigger?** Mention it in an issue and we can figure out the right approach together.

## Code of Conduct

This project welcomes contributors from all backgrounds. We expect:
- **Respectful** — Treat others with kindness
- **Inclusive** — Welcome diverse perspectives and experiences
- **Professional** — Focus on the work, not personal attacks
- **Constructive** — Criticism should be specific, actionable, and aimed at improvement

If something feels off, open an issue or reach out directly.

Examples of unacceptable behavior:
- Harassment, discrimination, or exclusion
- Off-topic arguments or spam
- Attacks on anyone's character

If you experience or witness unacceptable behavior, please tell the project maintainers.

## Licensing

By contributing to this repository, you agree that your contributions are licensed under the MIT License (the same as this project). Your copyright is retained; you're simply granting the project a license to use your work.

---

**Thank you for supporting better documentation for Nanobot!** 🚀
