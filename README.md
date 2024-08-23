# nvm-auto-use

**nvm-auto-use** is a utility script that automatically switches to the correct **Node.js** version when entering a project directory containing a `.nvmrc` file. It ensures you're always using the right Node.js version for your projects.

## Features
- Auto-switches Node.js versions based on the `.nvmrc` file.
- Installs the specified Node.js version if not already installed.
- Reverts to the default Node.js version if no `.nvmrc` file is found.

## Requirements

### Dependencies
Before using **nvm-auto-use**, ensure the following dependencies are installed:

- **Zsh**: The script is designed to work with Zsh. You can install Zsh on most systems via package managers:

    ```bash
    # On Ubuntu/Debian
    sudo apt-get install zsh

    # On macOS (using Homebrew)
    brew install zsh
    ```

- **nvm (Node Version Manager)**: nvm must be installed to manage Node.js versions. You can install nvm by running:

    ```bash
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
    ```

- **pyenv**: The script references `pyenv` in the PATH setup. Ensure pyenv is installed if you plan to use it:

    ```bash
    curl https://pyenv.run | bash
    ```

### Installation

1. Add the following lines to your `.zshrc` file:

    ```bash
    export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
    PATH=$(pyenv root)/shims:$PATH

    # NVM Auto load version
    autoload -U add-zsh-hook  
    load-nvmrc() {
      local node_version="$(nvm version)"
      local nvmrc_path="$(nvm_find_nvmrc)"

      if [ -n "$nvmrc_path" ]; then
        local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

        if [ "$nvmrc_node_version" = "N/A" ]; then
          nvm install
        elif [ "$nvmrc_node_version" != "$node_version" ]; then
          nvm use
        fi
      elif [ "$node_version" != "$(nvm version default)" ]; then
        echo "Reverting to nvm default version" 
        nvm use default
      fi
    }
    add-zsh-hook chpwd load-nvmrc
    load-nvmrc
    ```

2. Reload your Zsh configuration:

    ```bash
    source ~/.zshrc
    ```

## Usage
Once installed, **nvm-auto-use** will automatically switch Node.js versions based on the `.nvmrc` file in the current directory. If no `.nvmrc` is found, it defaults to the global Node.js version.

## License
This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.
