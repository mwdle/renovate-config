# Renovate Bot Docker Compose Configuration

This repository contains the Docker Compose configuration and Jenkins pipeline to run Renovate Bot as a scheduled automated job. It scans our internal Gitea repositories and creates Pull Requests for dependency updates based on our centralized configuration rules.

## Table of Contents

- [Description](#renovate-bot-docker-compose-configuration)
- [Getting Started](#getting-started)
- [License](#license)
- [Disclaimer](#disclaimer)

## Getting Started

1. **Credentials:** Create a Jenkins 'Secret file' credential named `renovate-config.env` (use `.env.example` as a template) containing your Gitea hostname, organization, and Personal Access Token (PAT).
2. **Central Config:** Ensure the `renovate-config` repository exists in your Gitea organization with a valid `default.json` rule set.
3. **Execution:** The pipeline runs automatically on the cron schedule defined in the `Jenkinsfile`. You can also trigger it manually from the Jenkins UI.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Disclaimer

This repository is provided as-is and is intended for informational and reference purposes only. The author assumes no responsibility for any errors or omissions in the content or for any consequences that may arise from the use of the information provided. Always exercise caution and seek professional advice if necessary.
