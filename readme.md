# Junie — AI Coding Agent now lives on GitHub 🚀

Welcome to the **Early Access Program** for Junie for GitHub.

Junie is a coding agent by JetBrains that redefines how you code.  
We designed it to work in close collaboration with developers — handling routine and complex tasks both in your IDE and
right here, on GitHub.

---

## ✅ How to Enable Junie

### Install the GitHub App

Simply install the [Junie GitHub App](https://github.com/apps/jetbrains-junie) — setup is automatic! 💫

### What Happens After Installation

Once installed, Junie will automatically:

- Create a Pull Request with the required workflow file (`.github/workflows/ej-issue.yml`)
- Add a `devcontainer.json` file to your repository to support containerized environments

You can review and adjust the `devcontainer.json` file if needed, but **no manual configuration is required** to get
started.

> Junie is currently in closed Early Access.  
> To join, please visit our [Discord](https://jb.gg/junie/github) or ask to be added to the whitelist.

---

### 📝 Manual Setup (optional)

If needed, you can configure Junie manually by adding the following file to `.github/workflows/ej-issue.yml`:

<details>
<summary>Click to view the workflow file</summary>

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

</details>

---

## How Junie Works

### Trigger Junie from Issues

1. Create an **Issue** with the word `junie` in the title.
2. Or add a **Comment** with `@jetbrains-junie` to an existing issue.

> _Note: File attachments are not yet supported. Please provide your request in plain text._

---

### Pull Requests with Junie

Junie helps you iterate on pull requests with smart suggestions:

- Comments with `@jetbrains-junie` are picked up and processed.
- If part of a **code review**, Junie waits until the review is submitted and picks up all comments with the mention
- If the PR was created by Junie or the author of comments with mentions, fixes are committed to the same branch,
  otherwise, Junie creates a **new PR** with the changes.
- Parallel runs on the same PR branch are prevented.

> _Note: Advanced configuration options (like custom keywords or target branches) are coming soon._

---

## 🔧 Other Goodies

- Join our [Discord](https://jb.gg/junie/github) to share your feedback and get help from the team.
- Try Junie in your IDE: [JetBrains Plugin Marketplace](https://plugins.jetbrains.com/plugin/26104-jetbrains-junie-eap).
