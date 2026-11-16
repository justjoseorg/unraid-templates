# Unraid templates

## Copilot CLI

A password-protected browser terminal with GitHub Copilot CLI, GitHub CLI,
and persistent configuration, projects and additional Alpine packages.
The Docker image and its build workflow live in
[copilot-cli-unraid](https://github.com/justjoseorg/copilot-cli-unraid).

### Installation

The image package must be public on GHCR before Unraid can pull it without
registry credentials.

From the Unraid host terminal, install the template:

```sh
mkdir -p /boot/config/plugins/dockerMan/templates-user
curl --fail --location \
  https://raw.githubusercontent.com/justjoseorg/unraid-templates/main/templates/copilot-cli.xml \
  --output /boot/config/plugins/dockerMan/templates-user/my-copilot-cli.xml
```

In **Docker > Add Container**, select the `copilot-cli` user template.
Set a strong **Web terminal password** and review the paths and port:

| Setting | Default |
| --- | --- |
| Web terminal | Port `7681`, username `terminal` |
| Persistent configuration | `/mnt/user/appdata/copilot-cli` -> `/config` |
| Project workspace | `/mnt/user/appdata/copilot-cli/workspace` -> `/workspace` |

Open the container's **WebUI**, enter the terminal credentials, then run:

```sh
copilot login --device-code
copilot
```

Copilot requires a GitHub account with CLI access. For GitHub CLI operations,
also run `gh auth login --web`. Authentication is stored under `/config`;
do not publish or share that directory.

The terminal runs as root inside the container. Only mount the projects it
should access; do not add the Docker socket or the host root filesystem.
Keep the web port on a trusted LAN or VPN. Use TLS through a reverse proxy
when appropriate; HTTP basic authentication alone does not encrypt traffic.

Use `apk add PACKAGE` in the terminal to install additional packages.
They are reinstalled from `/config/apk-packages` after container recreation.
See the image repository for limitations and update instructions.

## License and trademarks

Template code is MIT licensed. The included Copilot identification logo was
provided with the original project and is sourced from
[Wikimedia Commons](https://commons.wikimedia.org/wiki/File:GitHub_Copilot_logo.svg).
GitHub Copilot is a GitHub trademark. This project is not affiliated with or
endorsed by GitHub.
