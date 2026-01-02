

                                      00      0        0       0         00000        000      00000         00       0                  000000       0        0      00        0      0     00000                                      
                                     0000   00000    00000    000       000000000  000000000 000000000     00000     0000  0000       0000000000    00000    00000   0000 0000 0000   000 000000000                                     
                                     00000 000000    000000   000       000   000 0000   000        000    000000    00000 0000       0000         0000000   000000 00000 0000 00000  000 000                                           
                                     000000000000   000 000   000       000   000 0000   000 000000000    00000000   0000000000       0000000000   000 000   000000000000 0000 000000 000 000000000                                     
                                     00 00000 000  000000000  000       000   000 0000   000 0000 0000   000000000   00 0000000       0000   000  000000000  000000000000 0000 000 000000 000    00                                     
                                     00  0000 000 000000 0000 000000000 000000000 0000000000 0000  0000  00000  000  00  000000       0000000000 000000 000  000 0000 000 0000 000  00000 000000000                                     
                                     00       00  000     00    000000   000000      00000    00    000  00      00  00    00           000000    00     00   00       00  00  00    000    000000                                      



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

Set up the following **Secrets** and **Variables** in your GitHub repository settings:

### GitHub Secrets
| Secret | Description |
| :--- | :--- |
| `BUTLER_API_KEY` | Your Itch.io Butler API key ([Get it here](https://itch.io/docs/butler/login.html)) |
| `ITCH_USER` | Your Itch.io username |
| `ITCH_GAME` | Your Itch.io game slug (e.g., `my-awesome-game`) |

### GitHub Variables
| Variable | Description | Example |
| :--- | :--- | :--- |
| `REPO_URL` | URL of the repository to clone | `https://github.com/org/repo.git` |
| `BUILD_FOLDER_NAME` | Unique folder name for the build workspace | `my-project-itch` |
| `BUILD_PROFILE` | Filename of the Unity Build Profile | `WebPlayer.unitybuildprofile` |
| `UNITY_EDITOR_VERSION` | Exact version installed on the runner | `6000.3.0f1` |
| `ITCH_CHANNEL` | Target Itch.io channel | `html` |

> [!NOTE]
> If you are using a basic/free GitHub account with a **private** repository, ensure these are set at the **Repository** level rather than the Organization level.

---

## How It Works

### Workflow Triggers
The workflow runs automatically when:
- Code is pushed to the `itchBuild` branch.
- A Pull Request is opened against the `itchBuild` branch.
- Manually triggered via "Workflow Dispatch".

### Jobs
1. **buildWithItchProfile**:
    - Cleans the temporary workspace on the C: drive.
    - Clones the repository recursively.
    - Verifies the Unity project structure.
    - Executes the Unity build using `-batchmode` and `-activeBuildProfile`.
    - Uploads the resulting files as a GitHub artifact.

2. **deployToItch**:
    - Downloads the build artifacts.
    - Downloads and extracts the `butler` executable.
    - Pushes the build to Itch.io using the `butler push` command with a version tag: `itchBuild-<run_number>`.

---

## Troubleshooting
- **Locked Files**: If the "Clean workspace" step fails, ensure no Unity Editor instances or file explorers are open in the `C:\temp\<BUILD_FOLDER_NAME>` directory on the runner.
- **Unity Path**: Ensure the `UNITY_EDITOR_VERSION` variable matches the folder name exactly as it appears in `C:\Program Files\Unity\Hub\Editor\`.
- **Build Failures**: Check the `build.log` file produced in the build output directory (also visible in the GitHub Actions console output).
