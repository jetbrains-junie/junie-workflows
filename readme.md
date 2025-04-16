# Junie — AI Coding Agent now lives on GitHub 🚀

Junie is a AI coding agent that helps you solve your tasks right from GitHub.
Simple setup. No drama.

## ✨ What Can Junie Do?

### Create PRs from Issues:
Add `junie` word to your issue title and describe the task — Junie will generate a Pull Request.

### Fix via Comments:
Comment `@jetbrains-junie fix` on an issue — Junie will patch the code on and create Pull Request.

## ✅ How to Enable

Install the [GitHub App](https://github.com/apps/jetbrains-junie), and we’ll do it for you 💫 
or add a workflow file `.github/workflows/ej-issue.yml`:

```yaml
name: Junie
run-name: Junie run ${{ inputs.run_id }}

permissions:
  contents: write
  pull-requests: write
  packages: read

on:
  workflow_dispatch:     
    inputs:
      run_id:
        description: "id of workflow process"
        required: true
      workflow_params:
        description: "stringified params"
        required: true

jobs:
  call-workflow-passing-data:
    uses: jetbrains-junie/junie-workflows/.github/workflows/ej-issue.yml@main
    with:
      workflow_params: ${{ inputs.workflow_params }}
```

## 🔧 Other Goodies

JetBrains IDE Plugin: [Install](https://www.jetbrains.com/junie/)

