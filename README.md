# Augment Review PR Issue Implementation GitHub Action

AI-powered validation of GitHub issue implementations in pull requests using Augment. This action automatically analyzes your PR to ensure it fully implements the requirements from referenced GitHub issues.

## Quick Start

### 1. Get Your Augment API Credentials

First, you'll need to obtain your Augment Authentication information from your local Auggie session:

Example session JSON:

```json
{
  "accessToken": "your-api-token-here",
  "tenantURL": "https://your-tenant.api.augmentcode.com"
}
```

There are 2 ways to get the credentials:

- Run `auggie tokens print`
  - Copy the JSON after `TOKEN=`
- Copy the credentials stored in your Augment cache directory, defaulting to `~/.augment/session.json`

> **⚠️ Security Warning**: These tokens are OAuth tokens tied to your personal Augment account and provide access to your Augment services. They are not tied to a team or enterprise. Treat them as sensitive credentials:
>
> - Never commit them to version control
> - Only store them in secure locations (like GitHub secrets)
> - Don't share them in plain text or expose them in logs
> - If a token is compromised, immediately revoke it using `auggie tokens revoke`

### 2. Set Up the GitHub Repository Secret

You need to add your Augment credentials to your GitHub repository:

#### Adding Secret

1. **Navigate to your repository** on GitHub
2. **Go to Settings** → **Secrets and variables** → **Actions**
3. **Add the following**:
   - **Secret**: Click "New repository secret"
     - Name: `AUGMENT_SESSION_AUTH`
     - Value: The JSON value from step 1

> **Need more help?** For detailed instructions on managing GitHub secrets, see GitHub's official documentation:
>
> - [Using secrets in GitHub Actions](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)

### 3. Create Your Workflow File

Add a new workflow file to your repository's `.github/workflows/` directory and merge it.

### Example Workflows

For complete workflow examples, see the [`example-workflows/`](./example-workflows/) directory which contains:

- **[Basic Issue Validation](./example-workflows/basic-issue-validation.yml)** - Simple setup for validating issue implementations on new PRs
- **[Custom Guidelines Validation](./example-workflows/custom-guidelines-validation.yml)** - Demonstrates custom validation guidelines usage
- **[On-Demand Validation](./example-workflows/on-demand-validation.yml)** - Triggered by adding the `validate_issues` label

Each example includes a complete workflow file that you can copy to your `.github/workflows/` directory and customize for your needs.

## Features

- **Automatic Issue Extraction**: Extracts GitHub issue references from PR commit messages
- **Issue Requirement Analysis**: Fetches and analyzes requirements from referenced issues
- **Implementation Validation**: Compares code changes against issue requirements
- **Gap Identification**: Identifies missing or incomplete implementations
- **Intelligent Feedback**: Generates specific, actionable feedback using AI
- **Context-Aware**: Understands your codebase structure and issue patterns
- **GitHub Integration**: Seamlessly posts validation results via GitHub review comments

## How It Works

1. **Extract Issue References**: Analyzes commit messages to find issue references (#123, fixes #456, etc.)
2. **Fetch Issue Details**: Retrieves issue descriptions, acceptance criteria, and requirements via GitHub API
3. **Analyze Requirements**: Extracts what functionality should be implemented from each issue
4. **Review Code Changes**: Analyzes the PR diff to understand what was actually implemented
5. **Compare & Validate**: Compares issue requirements against code changes to identify gaps
6. **Post Feedback**: Submits a GitHub review with inline comments on implementation gaps

## Supported Issue Reference Formats

The action recognizes these patterns in commit messages:

- `#123` - Simple issue reference
- `fixes #123`, `fix #123` - Closes issue when PR merges
- `closes #456`, `close #456` - Closes issue when PR merges
- `resolves #789`, `resolve #789` - Closes issue when PR merges
- `fixes #123, closes #456` - Multiple issues in one commit
- Case insensitive matching

## Inputs

| Input                  | Description                                                                                | Required | Example                                             |
| ---------------------- | ------------------------------------------------------------------------------------------ | -------- | --------------------------------------------------- |
| `augment_session_auth` | Augment session authentication JSON containing accessToken and tenantURL (store as secret) | Yes      | `${{ secrets.AUGMENT_SESSION_AUTH }}`               |
| `github_token`         | GitHub token with `repo` scopes                                                            | Yes      | `${{ secrets.GITHUB_TOKEN }}`                       |
| `pull_number`          | The number of the pull request being reviewed                                              | Yes      | `${{ github.event.pull_request.number }}`           |
| `repo_name`            | The full name (owner/repo) of the repository                                               | Yes      | `${{ github.repository }}`                          |
| `custom_guidelines`    | Custom validation guidelines to include in issue implementation reviews                    | No       | See [Custom Guidelines](#custom-guidelines) section |
| `model`                | Optional model name to use; passed directly to augment agent                               | No       | e.g. `sonnet4`, from `auggie --list-models`         |
| `rules`                | JSON array of rule file paths forwarded to augment agent as repeated `--rules` flags       | No       | `'[".augment/rules.md"]'`                           |
| `mcp_configs`          | JSON array of MCP config file paths forwarded as repeated `--mcp-config` flags             | No       | `'[".augment/mcp.json"]'`                           |

## Custom Guidelines

You can provide custom validation guidelines to tailor the issue implementation review to your team's specific needs. These guidelines will be added to the default validation criteria.

### Basic Usage

```yaml
- name: Validate Issue Implementation
  uses: augmentcode/issue-review-pr@v0
  with:
    augment_session_auth: ${{ secrets.AUGMENT_SESSION_AUTH }}
    github_token: ${{ secrets.GITHUB_TOKEN }}
    pull_number: ${{ github.event.pull_request.number }}
    repo_name: ${{ github.repository }}
    custom_guidelines: |
      - Ensure all acceptance criteria checkboxes in issues are addressed
      - Verify that security requirements mentioned in issues are implemented
      - Check that performance requirements from issues are met
      - Confirm that documentation updates requested in issues are included
      - Validate that breaking changes mentioned in issues have migration guides
```

## Permissions

The action requires the following GitHub token permissions:

```yaml
permissions:
  contents: read # To checkout the repository and read files
  pull-requests: write # To post validation comments
  issues: read # To fetch referenced issue details
```

## What Gets Validated

This action validates that PR implementations address the requirements from referenced issues:

### Validation Areas
- **Functional Completeness**: Core functionality requested in the issue
- **Acceptance Criteria**: All explicit acceptance criteria are met
- **Edge Cases**: Specific edge cases mentioned in the issue are handled
- **Technical Requirements**: APIs, data models, integrations specified in the issue
- **Non-Functional Requirements**: Performance, security, accessibility needs from the issue
- **Documentation**: Documentation updates requested in the issue

### Validation Principles
- **High-Confidence Gaps Only**: Focuses on clear, obvious missing implementations
- **Explicit Requirements**: Validates against explicitly stated requirements
- **Constructive Feedback**: Provides helpful, actionable feedback
- **Issue-Scoped**: Only validates against requirements in referenced issues

## Example Output

The action will post a GitHub review with:

1. **Summary**: Overview of which issues were validated and overall status
2. **Inline Comments**: Specific feedback on implementation gaps anchored to relevant code lines
3. **Issue References**: Clear links between feedback and issue requirements
4. **Actionable Guidance**: Concrete examples of what's missing

## Best Practices

1. **Reference Issues in Commits**: Use standard patterns like `fixes #123` in commit messages
2. **Write Clear Issues**: Include explicit requirements and acceptance criteria in issues
3. **Custom Guidelines**: Provide project-specific validation requirements
4. **Review Feedback**: Treat AI feedback as suggestions to review, not absolute requirements
5. **Iterate**: Use feedback reactions (👍/👎) to help improve future validations

## Limitations

- Cannot execute code or verify functional correctness
- Analysis quality depends on issue description clarity
- Subject to GitHub API rate limits
- Cannot access external systems or databases
- Validates requirement coverage, not code quality
- May miss implicit or unstated requirements

## Permissions

The action requires the following GitHub token permissions:

```yaml
permissions:
  contents: read # To checkout the repository and read files
  pull-requests: write # To post validation comments
  issues: read # To fetch referenced issue details
```

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Support

For issues or questions:
- GitHub Issues: [https://github.com/augmentcode/issue-review-pr/issues](https://github.com/augmentcode/issue-review-pr/issues)
- Documentation: [https://docs.augmentcode.com](https://docs.augmentcode.com)

