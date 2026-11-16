# Unraid templates

## Copilot CLI

A password-protected browser terminal with GitHub Copilot CLI, GitHub CLI,
Node.js, Python, a multi-terminal sidebar and persistent developer tools.
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
| Repositories / starting directory | `/mnt/user/Storage/repos` -> `/Storage/repos` |
| GPU device (optional) | `/dev/dri`; remove the device entry to disable |

Both **Web terminal username** and **Web terminal password** are customizable
in the template and in **Edit Container** later. They are browser credentials,
not GitHub credentials.

Use **New terminal** in the left sidebar to run multiple tasks, switch terminals
without interrupting them, rename them, or close them. All terminals share the
same Copilot/GitHub sign-in, permissions, files and tools; no separate accounts
are created. New terminals start in `/Storage/repos`. Each browser page manages
its own set; closing or reloading it ends those terminal connections.

**GPU device** optionally passes through an Intel/AMD GPU without privileged
mode. Remove this configuration entry if you do not want GPU access or the host
has no `/dev/dri`. Do not leave its value blank. Additional userspace libraries
may be required by your chosen workload; this does not install a compute stack.

Open the container's **WebUI**, enter the terminal credentials, then run:

```sh
copilot login --device-code
copilot
```

Copilot requires a GitHub account with CLI access. For GitHub CLI operations,
also run `gh auth login --web`. Authentication is stored under `/config`;
do not publish or share that directory.

The image provides an automatically unlocked Secret Service keyring.
Its encrypted files and default unlock secret both persist in `/config`;
protect and back up the entire directory. See the image repository for
separately mounting an unlock secret and upgrading from plaintext storage.

For an existing installation, retain your configured credentials when updating
the saved template. The old Appdata workspace is not moved or deleted.

The terminal runs as root inside the container. Only mount the projects it
should access; do not add the Docker socket or the host root filesystem.
Keep the web port on a trusted LAN or VPN. Use TLS through a reverse proxy
when appropriate; HTTP basic authentication alone does not encrypt traffic.

Use `apk add PACKAGE` in the terminal to install additional packages.
They are reinstalled from `/config/apk-packages` after container recreation.
Python is preinstalled and `pip install PACKAGE` uses the persistent shared
environment at `/config/python`. `npm install --global PACKAGE` persists
under `/config/npm`. Retain `/config` when updating the image.
Incompatible interpreter/architecture upgrades can require rebuilding native
dependencies.
See the image repository for limitations and update instructions.

## License and trademarks

Template code and the new original terminal icon are MIT licensed.
The legacy Copilot identification logo (`assets/copilot-logo.svg`) was
provided with the original project and is sourced from
[Wikimedia Commons](https://commons.wikimedia.org/wiki/File:GitHub_Copilot_logo.svg).
GitHub Copilot is a GitHub trademark. This project is not affiliated with or
endorsed by GitHub.
