# custom-bashrc-profile

A customized `.bashrc` for Ubuntu/Debian systems featuring a colored prompt:

- **Username** in red
- **Hostname** (computer name) in green
- Directory in blue
- Force color support for most terminals

Includes the standard Ubuntu `.bashrc` features (history, aliases, completion, dircolors, etc.).

## Features

- Red username + green hostname in `PS1`
- `force_color_prompt=yes` so colors work reliably
- Compatible with most modern terminal emulators (gnome-terminal, etc.)
- Bonus: `sudo-timeout` helper for 30-minute cross-terminal sudo credential caching

## Installation

### One-liner (recommended)

```bash
curl -fsSL https://raw.githubusercontent.com/berchapman/custom-bashrc-profile/main/bashrc -o ~/.bashrc && source ~/.bashrc
```

**Backup first** (strongly recommended):

```bash
cp ~/.bashrc ~/.bashrc.backup-$(date +%s)
```

### Clone + install

```bash
git clone https://github.com/berchapman/custom-bashrc-profile.git ~/custom-bashrc-profile
cp ~/custom-bashrc-profile/bashrc ~/.bashrc
source ~/.bashrc
```

## Sudo Cache Policy (optional, 30 minutes, cross-terminal)

This repo also includes a ready-to-use sudoers snippet for extending sudo
password caching and sharing the timestamp across terminal contexts for the same
user. That means one successful `sudo -v` in Ghostty can also satisfy later
non-interactive checks such as `sudo -n true` for up to 30 minutes.

Ubuntu 26.04 may default to `sudo-rs`, which does not support the global
timestamp option used here. CommandNode switches the `sudo` alternative to the
classic sudo implementation (`/usr/bin/sudo.ws`) before installing this policy.
For manual installs, make the same switch first if `sudo -V` prints `sudo-rs`.

```bash
cd ~/custom-bashrc-profile
sudo update-alternatives --set sudo /usr/bin/sudo.ws
sudo install -m 440 sudo-timeout /etc/sudoers.d/commandnode-sudo-cache
sudo visudo -c -f /etc/sudoers.d/commandnode-sudo-cache && echo "30-minute global sudo cache enabled"
```

Or manually:

```bash
{
  echo 'Defaults timestamp_timeout=30'
  echo 'Defaults timestamp_type=global'
} | sudo tee /etc/sudoers.d/commandnode-sudo-cache
sudo chmod 440 /etc/sudoers.d/commandnode-sudo-cache
sudo visudo -c -f /etc/sudoers.d/commandnode-sudo-cache
```

Verify:

```bash
sudo -v
sudo -n true && echo "sudo cache is available to non-interactive commands"
```

Use `sudo -k` to clear the cached credential before the 30-minute timeout.

## Preview

Typical prompt after install:

**red**user**@**green**hostname**:**blue**~/path**$**

(Exact colors depend on your terminal theme.)

## Customization

Edit the `PS1` line inside the `bashrc` file:

```bash
PS1='${debian_chroot:+($debian_chroot)}\[\033[01;31m\]\u\[\033[00m\]@\[\033[01;32m\]\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
```

Common ANSI codes:
- 31 = red
- 32 = green
- 34 = blue
- 01 = bold

## Notes

- This is based on the default Ubuntu 26.04 `.bashrc`
- The grok-specific lines (if present on your machine) were intentionally excluded
- Works for interactive non-login shells (standard for terminals)
- For login shells, `~/.profile` on Ubuntu sources `~/.bashrc`

## Future updates

```bash
cd ~/custom-bashrc-profile
git pull
cp bashrc ~/.bashrc
source ~/.bashrc
```
