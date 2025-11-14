# GitHub Automation Script Explanation

## Auto Label Issues Workflow

This repository includes a GitHub Actions workflow that automatically labels new issues based on keywords in the title and body.

### How it works:

1. **Trigger**: The workflow runs when issues are opened or edited
2. **Logic**: 
   - Labels issue as "bug" if it contains the word "error" (case-insensitive)
   - Labels issue as "feature" if it contains the word "add" (case-insensitive)
   - Can apply multiple labels if both keywords are present

### Workflow file: `.github/workflows/auto-label-issues.yml`

```yaml
name: Auto Label Issues

on:
  issues:
    types: [opened, edited]

jobs:
  label-issues:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Auto-label issues
        uses: actions/github-script@v7
        with:
          script: |
            const issue = context.payload.issue;
            const title = issue.title.toLowerCase();
            const body = issue.body ? issue.body.toLowerCase() : '';
            
            const labels = [];
            
            // Check for bug keywords
            if (title.includes('error') || body.includes('error')) {
              labels.push('bug');
            }
            
            // Check for feature keywords
            if (title.includes('add') || body.includes('add')) {
              labels.push('feature');
            }
            
            // Add labels if any were found
            if (labels.length > 0) {
              await github.rest.issues.addLabels({
                owner: context.repo.owner,
                repo: context.repo.repo,
                issue_number: issue.number,
                labels: labels
              });
              console.log(`Added labels: ${labels.join(', ')} to issue #${issue.number}`);
            }
```

### Test Results:

- Issue #1 "error test" → Labeled as "bug" ✅
- Issue #2 "feature adding requirements" → Labeled as "feature" ✅  
- Issue #3 "email feature adding error" → Labeled as both "bug" and "feature" ✅

### How to extend:

You can easily extend this by:
1. Adding more keyword checks
2. Creating additional labels
3. Modifying the logic for more complex patterns
4. Adding different trigger conditions