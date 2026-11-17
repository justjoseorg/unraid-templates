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
| Release channel | `stable`; choose `beta` for previews |
| Web terminal | Port `7681`, username `terminal` |
| Persistent configuration | `/mnt/user/appdata/copilot-cli` -> `/config` |
| Repositories / starting directory | `/mnt/user/Storage/repos` -> `/Storage/repos` |
| GPU device (optional) | `/dev/dri`; remove the device entry to disable |

Both **Web terminal username** and **Web terminal password** are customizable
in the template and in **Edit Container** later. They are browser credentials,
not GitHub credentials.

### Stable or beta

| Channel | Image tag | Source branch |
| --- | --- | --- |
| Stable (recommended default) | `:stable` | `main` |
| Beta (preview; may include unfinished changes) | `:beta` | `beta` |

The template includes Community Applications' native **stable / beta** branch
selector. When using the manually imported template above, choose the channel
by setting **Repository** in **Add Container** to
`ghcr.io/justjoseorg/copilot-cli-unraid:stable` or
`ghcr.io/justjoseorg/copilot-cli-unraid:beta`.

To switch an existing container, use **Edit Container**, change just that
image tag, and apply. Retain your paths and credentials, back up `/config`
before switching, and expect running terminals/jobs to stop during recreation.
For side-by-side testing, use different container names, host ports and
`/config` directories; do not share one configuration directory.

Use **New terminal** in the shared left sidebar to run multiple tasks, switch
terminals without interrupting them, rename them, or close them. All terminals
share the same Copilot/GitHub sign-in, permissions, files and tools. Selecting
an existing terminal from another device resumes that exact shell/CLI process.

Every new browser page initially creates a fresh shell in `/Storage/repos`.
Untouched shells expire after **60 seconds**. Interacted-with shells remain
for **24 hours after activity**; running foreground/background jobs always
prevent automatic removal, and completed jobs get a fresh retention period.
The template exposes both timeouts in seconds (`0` disables expiry).
The WebUI also has an editable minutes dropdown beside **Unused shells clear
automatically**. Presets or custom nonnegative numbers (including fractions)
set the unused-shell delay; `0` disables it. Changes apply across devices and
persist in `/config/terminal-settings.json`, overriding the template's unused
shell grace value. Previously used shells retain their separate 24-hour limit.
Use **Hide** to collapse the sidebar and the circular **>_** header button to
restore it without reconnecting the active terminal.
Closing/reloading the browser only detaches; the **x** button closes the shell
on all devices. A container restart/update still ends its running processes.

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

### Image updates

The template defaults to `ghcr.io/justjoseorg/copilot-cli-unraid:stable`.
Pushes to the image repository's `main` branch update `:stable` and `:latest`
after checks pass. Pushes to `beta` update `:beta` without changing stable.
Existing `:latest` containers require no tag change and remain on stable.
Keep **Repository** on `:stable`, `:beta` or `:latest`; a pinned commit tag or
digest will not follow these updates.
Template-only edits do not rebuild the image or change an installed container's
saved settings.

Once the image repository's **Publish container image** workflow succeeds,
select **Docker > Check for Updates** on Unraid and apply the `copilot-cli`
update. No new template import is needed for an image-only update.

To install updates automatically, install **CA Auto Update Applications**
from Apps, enable updates for `copilot-cli`, and choose a schedule on your
Unraid host. The template cannot enable that host-side setting. Updating
restarts the container and ends running terminals and jobs, so choose an idle
maintenance window. Keep your `/config` and repositories mappings unchanged.

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
