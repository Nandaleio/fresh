# Troubleshooting

## Terminal Color Support

Fresh automatically detects your terminal's color capability and converts theme colors accordingly.

### Color Modes

- **Truecolor (24-bit)**: Full RGB color support. Used by modern terminals (Kitty, Alacritty, iTerm2).
- **256 colors**: Extended palette. Used by xterm-256color and similar.
- **16 colors**: Basic ANSI colors. Used by the Linux console.

### Terminal Multiplexers

GNU Screen and tmux can affect color rendering:
- **GNU Screen**: Does not support truecolor. Fresh uses 256 colors.
- **tmux**: Supports 256 colors by default; some configs support truecolor with `TERM=tmux-direct`.

### Manual Override

If colors look wrong, you can force a specific color mode with the `FRESH_COLOR_MODE` environment variable:

```bash
# Force 256-color mode (recommended for GNU Screen)
FRESH_COLOR_MODE=256 fresh

# Force 16-color mode
FRESH_COLOR_MODE=16 fresh

# Force truecolor (if auto-detection is wrong)
FRESH_COLOR_MODE=truecolor fresh
```

### Common Issues

| Symptom | Likely Cause | Solution |
| :--- | :--- | :--- |
| Colors look completely wrong | Truecolor detected but not supported | Use `FRESH_COLOR_MODE=256` |
| Weird artifacts/rendering issues | Terminal multiplexer interference | Try `FRESH_COLOR_MODE=256` or check TERM |
| Very limited/ugly colors | 16-color mode detected | Check your terminal supports 256 colors |
