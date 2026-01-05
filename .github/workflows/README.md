# MALDORAN GAMING

# Unity Itch.io Deployment Workflow (Self-Hosted Windows)

This GitHub Actions workflow automates the process of building a Unity project and deploying it to Itch.io. It is specifically designed to run on **self-hosted Windows runners**, allowing you to utilize local hardware and pre-installed Unity Editor versions.

## Features
- **Self-Hosted Optimization**: Uses local file paths (C:\temp) for high-speed builds and cleanup.
- **Unity Build Profiles**: Leverages the Unity Build Profile system (Unity 6+) for configuration.
- **Automated Butler Management**: Downloads and installs the latest Itch.io Butler tool during the deployment phase.
- **Artifact Storage**: Saves build outputs as GitHub artifacts for 14 days.

---

## Prerequisites

### 1. Self-Hosted Runner
You must have a Windows self-hosted runner configured.
- [GitHub Guide: Adding self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners/adding-self-hosted-runners)

### 2. Unity Installation
The runner machine must have the Unity Editor installed at the standard path:
`C:\Program Files\Unity\Hub\Editor\<VERSION>\Editor\Unity.exe`

---

## Configuration

This workflow is designed as a **reusable workflow**. When calling it from another action, you must provide the following **Inputs** and **Secrets**:

### Required Secrets
| Secret | Description |
| :--- | :--- |
| `BUTLER_API_KEY` | Your Itch.io Butler API key ([Get it here](https://itch.io/docs/butler/login.html)) |
| `ITCH_USER` | Your Itch.io username |
| `ITCH_GAME` | Your Itch.io game slug (e.g., `my-awesome-game`) |

### Required Inputs
| Input | Description | Example |
| :--- | :--- | :--- |
| `REPO_URL` | URL of the repository to clone | `https://github.com/org/repo.git` |
| `BUILD_FOLDER_NAME` | Unique folder name for the build workspace | `my-project-itch` |
| `BUILD_PROFILE` | Filename of the Unity Build Profile | `WebPlayer.unitybuildprofile` |
| `UNITY_EDITOR_VERSION` | Exact version installed on the runner | `6000.3.0f1` |
| `ITCH_CHANNEL` | Target Itch.io channel | `html` |

---

## How It Works

### Workflow Triggers
This workflow is triggered via `workflow_call`. To use it, create a caller workflow in your repository (e.g., `.github/workflows/main.yml`):

```yaml
name: Deploy To Itch Pipeline

on:
  push:
    branches: [ "itchBuild" ]
  workflow_dispatch:

jobs:
  call-itch-workflow:
    uses: Maldoran-Gaming/Maldoran-Gaming-Utilities/.github/workflows/mg_itch.yaml@main
    with:
      REPO_URL: ${{ vars.REPO_URL }}
      BUILD_FOLDER_NAME: ${{ vars.BUILD_FOLDER_NAME }}
      BUILD_PROFILE: ${{ vars.BUILD_PROFILE }}
      UNITY_EDITOR_VERSION: ${{ vars.UNITY_EDITOR_VERSION }}
      ITCH_CHANNEL: ${{ vars.ITCH_CHANNEL }}
    secrets: inherit
