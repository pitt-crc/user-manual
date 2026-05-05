# SSH Terminal Access

SSH ([Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell)) provides encrypted command-line access to the CRCD login nodes.

## Prerequisites

- Active [GlobalProtect VPN](https://services.pitt.edu/TDClient/33/Portal/KB/ArticleDet?ID=3426) connection
- A terminal application (see below)

## Terminal Applications

| Platform | Application | Notes |
|----------|-------------|-------|
| Windows | [MobaXterm](https://mobaxterm.mobatek.net/) | Includes built-in X server |
| Windows | [PuTTY](https://www.putty.org/) | Lightweight SSH client |
| macOS | Terminal (built-in) | In Applications/Utilities |
| macOS | [iTerm2](https://iterm2.com/) | Feature-rich alternative |

For GUI applications (X11 forwarding), you also need an X server:

- **Windows**: MobaXterm includes one; otherwise install [VcXsrv](https://sourceforge.net/projects/vcxsrv/)
- **macOS**: Install [XQuartz](https://www.xquartz.org/)

## Connection Details

| Setting | Value |
|---------|-------|
| Protocol | SSH |
| Hostname | `h2p.crc.pitt.edu` or `htc.crc.pitt.edu` |
| Username | Your Pitt username (lowercase) |
| Password | Your Pitt password |

## Connecting

```commandline
ssh -X username@h2p.crc.pitt.edu
```

The `-X` flag enables X11 forwarding for GUI applications. Replace `username` with your Pitt username in lowercase.

**Successful connection on MobaXterm:**

![MobaXterm](../_assets/img/web-portals/MobaXterm.png)

**Successful connection on macOS Terminal/iTerm2:**

![iTerm2](../_assets/img/web-portals/iTerm2.png)

## Definitions

| Term | Description |
|------|-------------|
| Terminal emulator | Software providing a text-based interface to type commands without a GUI |
| Linux shell | Text-based interpreter for user commands. CRCD supports `bash` (default) and `csh` |
| Command line | The shell prompt where you input commands to the remote host |
