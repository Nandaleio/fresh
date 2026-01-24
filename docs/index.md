# Getting Started

## Installation

Fresh can be installed through several package managers or built from source.

### Package Managers

**Homebrew (macOS/Linux):**
```bash
brew tap sinelaw/fresh && brew install fresh-editor
```

**Arch Linux (AUR):**
```bash
yay -S fresh-editor
```

**npm:**
```bash
npm install -g @fresh-editor/fresh-editor
```

**Cargo:**
```bash
cargo install fresh-editor
```

### From Source

See the [installation guide on GitHub](https://github.com/sinelaw/fresh?tab=readme-ov-file#installation) for building from source.

## Running Fresh

Once installed, you can run Fresh in several ways:

```bash
# Open an empty buffer
fresh

# Open a file
fresh src/main.rs

# Open multiple files
fresh file1.txt file2.txt
```

## Core Concepts

### The Command Palette

The command palette is your central hub for accessing all of Fresh's features. Press `Ctrl+P` to open it, and then start typing to search for commands.

### Buffers

Each open file is represented as a buffer. You can have multiple buffers open at once and switch between them.

### Splits

You can split your editor view horizontally or vertically to view multiple buffers at once.

### The Status Bar

The status bar at the bottom displays information about the current buffer, including the file name, cursor position, and Git branch.

## Next Steps

- Read the [User Guide](./guide/) for detailed usage instructions
- Learn about [terminal integration](./features/terminal) for shell commands
- Explore [plugin development](./development/plugin-development) to extend Fresh's functionality
