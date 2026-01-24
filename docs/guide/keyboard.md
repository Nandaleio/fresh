# Keyboard Configuration

Many OSes, window managers and terminal applications capture keys and filter them out so that applications like Fresh, running in the terminal, don't actually have a chance to handle those keys.

## Linux: XFCE Window Manager

### Disabling Workspace Switching Shortcuts

Follow these steps to clear the **Ctrl + Alt + Up** and **Ctrl + Alt + Down** shortcuts so they can be used in other applications (like `fresh`).

1.  **Open Settings**: Open the XFCE Application Menu and go to **Settings** > **Window Manager**.
2.  **Navigate to Keyboard**: Click on the **Keyboard** tab.
3.  **Find Workspace Shortcuts**: Scroll through the list of actions to find `Upper workspace` and `Bottom workspace`.
4.  **Clear Shortcuts**: Select each action and click the **Clear** button.
5.  **Close**: Click **Close** to save the changes.

## macOS Terminal Configuration

### Shift + Arrow Key Shortcuts in Terminal.app

Follow these steps to map **Shift + Up** and **Shift + Down** to specific escape sequences in your macOS Terminal.

1.  **Open Settings**: Launch Terminal and go to **Terminal** > **Settings** (or press `Cmd + ,`).
2.  **Navigate to Keyboard**: Click the **Profiles** tab, then select the **Keyboard** sub-tab.
3.  **Add First Shortcut (Cursor Up)**:
    * Click the **Plus (+)** icon.
    * **Key**: Select `Cursor Up`.
    * **Modifier**: Select `Shift`.
    * **Action**: Select `Send Text`.
    * **Input**: Type `\033[1;2A`
4.  **Add Second Shortcut (Cursor Down)**:
    * Click the **Plus (+)** icon.
    * **Key**: Select `Cursor Down`.
    * **Modifier**: Select `Shift`.
    * **Action**: Select `Send Text`.
    * **Input**: Type `\033[1;2B`

### Using the macOS Keymap

Fresh includes a dedicated macOS keymap that addresses terminal-specific challenges. To use it, add to your `~/.config/fresh/config.json`:

```json
{
  "keymap": "macos"
}
```

The macOS keymap is designed around these constraints:
- **Ctrl+Shift combinations don't work** in most macOS terminals.
- **Some Ctrl keys are ASCII control characters** (Ctrl+J, Ctrl+M, Ctrl+I).
- **International keyboards use Alt for essential characters.**
- **Unix readline conventions are preserved** (Ctrl+Y, Ctrl+K, Ctrl+U).

### Recommended Terminal Emulators

For the best experience with Fresh on macOS, use a terminal that supports the **Kitty Keyboard Protocol (KKP)** or **CSI u**:

| Terminal | KKP Support | Notes |
| :--- | :--- | :--- |
| **Kitty** | Full | Best keyboard handling |
| **Ghostty** | Full | Modern, fast |
| **WezTerm** | Full | Highly configurable |
| **Alacritty** | Full | GPU-accelerated |
| **iTerm2** | CSI u | Enable "Report modifiers using CSI u" in Preferences |

### Mission Control Conflicts

macOS uses **Ctrl+Arrow** keys for Mission Control by default. To use these in Fresh:

1. Open **System Settings** → **Keyboard** → **Keyboard Shortcuts** → **Mission Control**.
2. Disable or rebind the "Move left/right a space" and "Mission Control" shortcuts.

Alternatively, Fresh's macOS keymap provides **Alt+Arrow** as the primary word movement binding.

### Option Key as Meta

For Alt-based shortcuts to work, your terminal must send the Option key as an escape sequence:

**iTerm2:** Set "Left Option Key" to "Esc+" in Profiles → Keys → General.
**Terminal.app:** Check "Use Option as Meta Key" in Profiles → Keyboard.
