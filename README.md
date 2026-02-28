# devpod-template
Templating repository for DevOps project using DevPod with zsh and neovim

How to use

```bash
chmod +x .devcontainer/scripts/initialize-command.sh
devpod up --devcontainer-path .devcontainer/devcontainer.json --ide vscode --dotfiles https://github.com/dewwripper/dotfiles --dotfiles-script .setup-no-install.sh .
```