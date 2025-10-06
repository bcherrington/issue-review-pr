# Example Workflows

This directory contains example GitHub Actions workflow files demonstrating different ways to use the Augment Review PR Issue Implementation action.

## Available Examples

### 1. Basic Issue Validation
**File**: [`basic-issue-validation.yml`](./basic-issue-validation.yml)

The simplest setup - validates issue implementations on all new PRs and updates.

**When it runs**:
- When a PR is opened
- When new commits are pushed to a PR
- When a PR is reopened

**Use this when**: You want automatic validation on all PRs.

---

### 2. Custom Guidelines Validation
**File**: [`custom-guidelines-validation.yml`](./custom-guidelines-validation.yml)

Demonstrates how to add project-specific validation guidelines.

**Features**:
- Custom acceptance criteria validation
- Security requirement checks
- Performance requirement validation
- Documentation requirement verification
- Testing requirement checks
- Breaking change validation

**Use this when**: You have specific validation requirements for your project.

---

### 3. On-Demand Validation
**File**: [`on-demand-validation.yml`](./on-demand-validation.yml)

Triggered only when you add the `validate_issues` label to a PR.

**Features**:
- Manual trigger via label
- Automatically removes label after validation
- Useful for re-validation after fixes

**Use this when**: You want to control when validation runs or re-validate after addressing feedback.

---

## How to Use These Examples

1. **Choose an example** that fits your needs
2. **Copy the file** to your repository's `.github/workflows/` directory
3. **Customize** the workflow if needed (triggers, permissions, guidelines)
4. **Commit and push** the workflow file
5. **Ensure** you have set up the `AUGMENT_SESSION_AUTH` secret (see main [README](../README.md))

## Customization Tips

### Changing Triggers

You can modify when workflows run by changing the `on:` section:

```yaml
# Run only on specific branches
on:
  pull_request:
    branches:
      - main
      - develop

# Run only for specific file changes
on:
  pull_request:
    paths:
      - 'src/**'
      - 'lib/**'

# Run on draft PRs
on:
  pull_request:
    types: [opened, synchronize, reopened, ready_for_review]
```

### Adding Custom Guidelines

Customize the `custom_guidelines` input to match your project's needs:

```yaml
custom_guidelines: |
  - Ensure all database migrations mentioned in issues are included
  - Verify API versioning is updated if requested in the issue
  - Check that feature flags are added for new features
  - Confirm that monitoring/logging is added if requested
```

### Using Different Models

Specify a different AI model if needed:

```yaml
with:
  model: "sonnet4"  # or other available models
```

### Adding Rules Files

Include custom Augment rules:

```yaml
with:
  rules: '["augment/rules/validation.md", ".augment/project-rules.md"]'
```

## Combining with Other Actions

You can combine issue validation with other workflow steps:

```yaml
jobs:
  validate-and-test:
    runs-on: ubuntu-latest
    steps:
      # Run tests first
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Run tests
        run: npm test
      
      # Then validate issue implementation
      - name: Validate Issue Implementation
        uses: augmentcode/issue-review-pr@v0
        with:
          augment_session_auth: ${{ secrets.AUGMENT_SESSION_AUTH }}
          github_token: ${{ secrets.GITHUB_TOKEN }}
          pull_number: ${{ github.event.pull_request.number }}
          repo_name: ${{ github.repository }}
```

## Troubleshooting

### Workflow doesn't run
- Check that the workflow file is in `.github/workflows/`
- Verify the trigger conditions match your PR
- Check the Actions tab for any errors

### Authentication errors
- Ensure `AUGMENT_SESSION_AUTH` secret is set correctly
- Verify the token hasn't expired
- Check that `GITHUB_TOKEN` has the required permissions

### No validation feedback
- Verify that commits reference issues using supported patterns
- Check that referenced issues exist and are accessible
- Review the action logs for any errors

## Need Help?

- See the main [README](../README.md) for setup instructions
- Check [GitHub Actions documentation](https://docs.github.com/en/actions)
- Open an issue in the repository

