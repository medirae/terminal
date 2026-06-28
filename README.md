### Description
This repo contains my terminal shell scripts located at `~/Studio/Terminal`.

Be warned that since I mainly use zsh, there may be some code that is only zsh compatible, but I doubt that that's the case; usually my codes are bash-like.

### TODOS
- cleanup deprecated directory
- create documentation for the scripts.
    - Or dynamically add those documentation to here, or another relevant readme file.

# ZSH Optimization Framework

A highly efficient, modular configuration framework for organizing zsh code. Instead of stuffing thousands of lines of aliases, environment variables, and custom functions into a single monolithic `.zshrc`, this repository organizes them into a clean, nested folder structure and utilizes Zsh's native word-code (`.zwc`) compilation for blazing-fast shell startup times.

---

## 📂 Directory Structure

The framework scans and aggregates files dynamically based on their specific directory and extension:

```text
terminal/
├── aliases/          # App/tool aliases (Scans for *.zsh)
├── env/              # Environment variables and exports (Scans for *.env)
├── modules/          # Complex functions, logic, and configurations (Scans for *.rc)
├── src/              # Core framework compilation and evaluation engines
│   ├── compile.zsh
│   ├── eval-dev.zsh
│   └── eval.zsh
└── src.zsh           # Main entry point controller
```

## 🛠️ Installation & Setup

1. Clone the Repository

Clone this repository to your preferred location (e.g., your code studio or as a Zim framework module):

```bash
git clone https://github.com/medirae/terminal.git ~/Studio/Terminal
```

2. Configure Your .zshrc

I figured this out long ago but now I've forgotten. # TODO rewrite this part with more detail.

This is in my `~/.profile`:
```bash
if [ -d "$HOME"/.zim/modules/terminal ]; then
	source "$HOME"/.zim/modules/terminal/src.zsh env
elif [ -d "$HOME"/Studio/Terminal ]; then
	source "$HOME"/Studio/Terminal/src.zsh env
fi
```

`~/.zshenv`:
```bash
source "$HOME"/.profile
```

`~/.zprofile`:
```bash
source "$HOME"/.zprofile
```

I've imported zim in my `~/.zshrc`.

and this is at the end of my `~/.zimrc`:
```conf
zmodule mrafee113/terminal --on-pull './src.zsh recompile' --cmd 'source {}/src.zsh aliases'
zmodule mrafee113/terminal --cmd 'source {}/src.zsh modules'
```

## 🎮 Usage and CLI Commands

* `source src.zsh env`: Sources all environment variables (\*.env) found in the /env directory.
* `source src.zsh aliases`: Sources all terminal aliases (\*.zsh) found in the /aliases directory.
* `source src.zsh modules`: Sources all functional scripts (\*.rc) found in the /modules directory.

### Other Commands

Whenever you add new files or modify existing ones in production, optimize your shell startup speed using these utility arguments.
Or whenever you intend to do fast-paced trial and error.

* `source src.zsh compile`: Compile/Recompile all altered scripts into high-performance .zwc formats
* `source src.zsh clean`: Deep clean and remove all generated .zwc binaries and cache directories
* `source src.zsh recompile`: A shortcut to sequentially wipe the cache and recompile everything from scratch
* `source src.zsh dev`: Evaluates your directories dynamically on the fly without looking for or building compiled .zwc caches. This is perfect for when you are actively writing new aliases or functions and want to test changes immediately across shell reloads without running compile every time.

## 🧠 Under the Hood: How Efficiency is Achieved

When you run source `src.zsh compile`, the framework optimizes your terminal setup through a distinct dual-phase compilation:
1. **Self-Optimization**: The framework checks if its core engine files (`src.zsh`, `src/compile.zsh`, and `src/eval.zsh`) have been modified. If they are newer than their compiled counterparts, it runs `zcompile -UR` on itself to ensure the framework itself runs at maximum speed.
2. **Isolated Workspace Compilation**: Rather than cluttering your clean functional directories with sidecar `.zwc` files, `src/compile.zsh` generates an isolated, hidden workspace mirror under:
`/<directory>/.zwc.d/`. Your nested file names are flattened into relative paths using double hyphens (e.g., `aliases/git/git-heavy.zsh` gets safely flattened and compiled as `.zwc.d/git--git-heavy.zsh.zwc)`. Zsh reads directly from this map during shell initialization, cutting out file system traversals entirely.
