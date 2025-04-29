# Junie — AI Coding Agent now lives on GitHub 🚀

Welcome to the **Early Access Program** for Junie for GitHub.

Junie is a coding agent by JetBrains which redefines how you code.
We designed it to work in close collaboration with a developer for routine and complex tasks, opening the new way to
code in both IDEs and outside the IDE, on GitHub.

## How to Enable

1. Install the [GitHub App](https://github.com/apps/jetbrains-junie), and we’ll handle setup for you 💫

   Junie will automatically create a Pull Request with the workflow file and install itself in your repository.
   Or manually add the following workflow file to `.github/workflows/ej-issue.yml`:

<details> <summary>Click to view the workflow file</summary>

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

2. Junie will automatically create a Pull Request with the workflow file and install itself in your repository.

3. It will also add a devcontainer.json file to help run Junie in a containerized environment.

4. Please review the devcontainer.json carefully and adjust it if needed. We recommend verifying it locally using VS
   Code or Docker.

> Note: Junie is currently in closed Early Access.
> 
> To get access, please join our [Discord](https://jb.gg/junie/github) or contact us to be added to the whitelist.

###  How Junie Works

Junie can be triggered in two ways:

1️⃣ By creating an Issue with the word `junie` in the title.

2️⃣ By posting a Comment containing `@jetbrains-junie` fix.

_Currently, file attachments are not supported — please only include text instructions._

### PR Improvement via Comments

* Junie listens for comments containing `@jetbrains-junie` fix.

* If the comment is part of a review (start review → add comments → submit review), Junie waits until the review is
  submitted before processing.

* If the comment is added directly to the conversation tab or as a single comment, Junie processes it immediately.

* If the PR was created by Junie or by the PR author themselves, commits are pushed directly to the branch.

* Otherwise, Junie creates a new Pull Request based on the current branch.

* After the workflow finishes, Junie replies to the PR with a summary and a link to the new PR (if created).

* Junie does not allow running multiple workflows simultaneously on the same branch — if already running, it responds
  with a message.

* Successfully processed comments are marked with a :done: emoji to prevent re-processing.

_Advanced configuration options will be available later, including custom trigger keywords and push strategies._

## 🔧 Other Goodies

Join our [Discord](https://jb.gg/junie/github) to share your experience and get help.

JetBrains IDE Plugin: [Install from Marketplace](https://plugins.jetbrains.com/plugin/26104-jetbrains-junie-eap).

