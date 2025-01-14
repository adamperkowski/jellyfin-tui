# jellyfin-tui

The goal of this project is a fully featured TUI client for Jellyfin. Inspired by CMUS and others, it's my attempt at creating a usable and feature-rich music player.

The player has a cover image in the corner, courtesy of the [ratatui-image](https://github.com/benjajaja/ratatui-image) crate. Most modern terminals should support sixel graphics or equivalent. The cover image and other features are optional and can be disabled in the configuration file (see below).

### Features
- streams your music from Jellyfin
- lyrics with autoscroll (from jellyfin 10.9)
- sixel cover image
- transcoding
- double queue with order control, etc.
- global/local search
- last.fm scrobbling
- vim keybindings
- MPRIS controls

### Planned features
- playlists (play/create/edit)
- jellyfin-wide remote control
- offline caching
- dashboard tab (recently played, new music etc.)
- switch to a custom backend (rodio?) and make mpv optional

### Screenshots
![image](.github/screen7112.png)

### Installation
Jellyfin-tui uses libmpv as the backend for audio playback. You need to have mpv installed on your system.

#### Linux
Linux is the main target OS for this project. You can install mpv from your package manager.
```bash
# add ~/.cargo/bin to your PATH (~/.bashrc etc.) if you haven't already
export PATH=$PATH:~/.cargo/bin/

# install mpv
sudo pacman -S mpv # arch
sudo apt install mpv libmpv-dev # ubuntu
```
```bash
# clone this repository
git clone https://github.com/dhonus/jellyfin-tui
cd jellyfin-tui

# checkout the latest stable version if desired
# (git pull and re-run to update)
git checkout $(git tag | tail -1)

cargo run --release

# or install
cargo install --path .
```

#### macOS
```bash
brew install mpv
git clone https://github.com/dhonus/jellyfin-tui
cd jellyfin-tui
export LIBRARY_PATH="$LIBRARY_PATH:$(brew --prefix)/lib"
export PATH=$PATH:~/.cargo/bin/
cargo install --path .
```
### Key bindings
|key|alt|action|
|---|---|---|
|space||play / pause|
|enter||start playing selected|
|up / down|k / j|navigate **up** / **down**|
|tab||cycle between **Artist** & **Track** lists|
|shift + tab||cycle further to **Lyrics** & **Queue**|
|a / A||skip to next / previous **album**, or next in Artists, alphabetically|
|F1, F2||switch tab >> F1 - **Library**, F2 - **Search**|
|F1|ESC|return to **Library** tab|
|left / right|r / s|seek +/- 5s|
|n||next track|
|N||previous track; if over 5s plays current track from the start|
|+ -||volume up / down|
|ctrl + e|ctrl + enter|play next|
|e|shift + enter|enqueue (play last)|
|E||clear queue|
|d||remove from queue|
|x||stop playback|
|t||toggle transcode (applies to newly added songs, not whole queue)|
|q|^C|quit|

### Configuration
When you run jellyfin-tui for the first time, it will ask you for the server address, username and password and save them in the configuration file.

The program **prints the config location** when run. On linux, the configuration file is located at `~/.config/jellyfin-tui/config.yaml`. Feel free to edit it manually if needed.
```yaml
# must contain protocol and port
server: 'http://localhost:8096'
username: 'username'
password: 'imcool123'

persist: false # don't restore session on startup
art: false # don't show cover image
auto_color: false # don't grab the primary color from the cover image
primary_color: '#7db757' # hex or color name ('green', 'yellow' etc.)

# options specified here will be passed to mpv - https://mpv.io/manual/master/#options
mpv:
  af: lavfi=[loudnorm=I=-16:TP=-3:LRA=4]
  no-config: true
  log-file: /tmp/mpv.log

transcoding:
  enabled: true
  bitrate: 128
  # container: mp3
```

### MPRIS
Jellyfin-tui registers itself as an MPRIS client, so you can control it with any MPRIS controller. For example, `playerctl`.

### Search

In the Artists and Tracks lists you can search by pressing '/' and typing your query. The search is case insensitive and will filter the results as you type. Pressing `ESC` will clear the search and keep the current item selected.

You can search globally by pressing `F2`. The search is case insensitive and will search for artists, albums and tracks. It will pull **everything** without pagination, so it may take a while to load if you have a large library. This was done because jellyfin won't allow me to search for tracks without an artist or album assigned, which this client doesn't support.

![image](.github/search.png)

### Supported terminals
Not all terminals have the features needed to cover every aspect of jellyfin-tui. While rare, some terminals lack sixel (or equivalent) image support or have certain key event limitations. The following are tested and work well:
- kitty (recommended)
- iTerm2 (recommended)
- alacritty
- konsole
