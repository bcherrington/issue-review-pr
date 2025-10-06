# Quick Start Guide

Get started with Augment Review PR Issue Implementation in 5 minutes.

## Prerequisites

- GitHub repository with issues and pull requests
- Augment account with API access
- GitHub repository admin access (to add secrets)

## Step 1: Get Augment Credentials (2 minutes)

### Option A: Using Auggie CLI
```bash
auggie tokens print
```
Copy the JSON after `TOKEN=`

### Option B: From Session File
```bash
cat ~/.augment/session.json
```

You should see something like:
```json
{
  "accessToken": "your-token-here",
  "tenantURL": "https://your-tenant.api.augmentcode.com"
}
```

## Step 2: Add GitHub Secret (1 minute)

1. Go to your repository on GitHub
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Name: `AUGMENT_SESSION_AUTH`
5. Value: Paste the JSON from Step 1
6. Click **Add secret**

## Step 3: Create Workflow File (2 minutes)

Create `.github/workflows/validate-issues.yml`:

```yaml
name: Validate Issue Implementation

on:
  pull_request:
    types: [opened, synchronize, reopened]

permissions:
  contents: read
  pull-requests: write
  issues: read

jobs:
  validate-issues:
    runs-on: ubuntu-latest
    name: Validate PR implements referenced issues
    steps:
      - name: Validate Issue Implementation
        uses: augmentcode/issue-review-pr@v0
        with:
          augment_session_auth: ${{ secrets.AUGMENT_SESSION_AUTH }}
          github_token: ${{ secrets.GITHUB_TOKEN }}
          pull_number: ${{ github.event.pull_request.number }}
          repo_name: ${{ github.repository }}
```

## Step 4: Commit and Push

```bash
git add .github/workflows/validate-issues.yml
git commit -m "feat: add issue implementation validation"
git push
```

## Step 5: Test It Out

1. Create a new issue in your repository (e.g., #123)
2. Add some requirements to the issue description
3. Create a new branch and make some changes
4. Commit with: `git commit -m "fixes #123: implement feature"`
5. Open a pull request
6. Watch the action run and post validation feedback!

## What Happens Next?

The action will:
1. ✅ Extract issue #123 from your commit message
2. ✅ Fetch the issue details from GitHub
3. ✅ Analyze the requirements in the issue
4. ✅ Compare your code changes to the requirements
5. ✅ Post a review comment with validation results

## Example Issue Format

For best results, write issues with clear requirements:

```markdown
## Description
Add user authentication to the application

## Acceptance Criteria
- [ ] Users can register with email and password
- [ ] Users can log in with credentials
- [ ] Users can log out
- [ ] Passwords are hashed before storage
- [ ] Invalid credentials show error message

## Technical Requirements
- Use bcrypt for password hashing
- Store user data in PostgreSQL
- Add JWT token authentication
```

## Example Commit Messages

Reference issues using these patterns:

```bash
# Simple reference
git commit -m "Add login form (#123)"

# Closing keywords
git commit -m "fixes #123: implement user authentication"
git commit -m "closes #456: add password hashing"
git commit -m "resolves #789: implement logout"

# Multiple issues
git commit -m "fixes #123, closes #456: complete auth system"
```

## Customization

### Add Custom Guidelines

Edit your workflow to include project-specific validation:

```yaml
- name: Validate Issue Implementation
  uses: augmentcode/issue-review-pr@v0
  with:
    augment_session_auth: ${{ secrets.AUGMENT_SESSION_AUTH }}
    github_token: ${{ secrets.GITHUB_TOKEN }}
    pull_number: ${{ github.event.pull_request.number }}
    repo_name: ${{ github.repository }}
    custom_guidelines: |
      - Ensure all acceptance criteria checkboxes are addressed
      - Verify security requirements are implemented
      - Check that tests are included for new features
```

### Change When It Runs

Only validate specific branches:
```yaml
on:
  pull_request:
    branches:
      - main
      - develop
```

Only validate when labeled:
```yaml
on:
  pull_request:
    types: [labeled]

jobs:
  validate-issues:
    if: github.event.label.name == 'validate_issues'
    # ... rest of job
```

## Troubleshooting

### "No issues referenced in commit messages"
- Make sure your commits include issue references like `#123` or `fixes #456`
- Check that you're using supported patterns (see examples above)

### "Issue not found or not accessible"
- Verify the issue number exists in your repository
- Check that the issue is not in a different repository
- Ensure the GitHub token has access to read issues

### Action doesn't run
- Verify the workflow file is in `.github/workflows/`
- Check that the trigger conditions match (e.g., PR opened)
- Look at the Actions tab for error messages

### Authentication errors
- Verify `AUGMENT_SESSION_AUTH` secret is set correctly
- Check that the token hasn't expired
- Try running `auggie tokens print` again to get a fresh token

## Next Steps

- 📖 Read the full [README](README.md) for detailed documentation
- 📝 Check out [example workflows](example-workflows/) for more patterns
- 🔧 Review [PROJECT_SUMMARY](PROJECT_SUMMARY.md) for architecture details
- 💬 Open an issue if you need help

## Tips for Success

1. **Write Clear Issues**: Include explicit requirements and acceptance criteria
2. **Use Checklists**: Checkbox lists in issues work great for validation
3. **Reference Issues**: Always reference issues in commit messages
4. **Review Feedback**: Treat AI suggestions as helpful recommendations
5. **Iterate**: Use 👍/👎 reactions to help improve future validations

## Common Patterns

### Feature Development
```markdown
Issue: Add search functionality
Commits: 
  - "feat: add search API endpoint (fixes #123)"
  - "feat: add search UI component (fixes #123)"
  - "test: add search tests (fixes #123)"
```

### Bug Fixes
```markdown
Issue: Fix login redirect bug
Commit: "fix: correct redirect after login (closes #456)"
```

### Multiple Issues
```markdown
Commits:
  - "feat: implement auth (fixes #123, #124)"
  - "docs: update auth documentation (closes #125)"
```

## Support

Need help? 
- 📧 Open an issue: https://github.com/augmentcode/issue-review-pr/issues
- 📚 Documentation: https://docs.augmentcode.com
- 💬 Community: Join our discussions

---

**Ready to validate your first PR?** Follow the steps above and you'll be up and running in minutes! 🚀

