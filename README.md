# GNOME time-based live wallpapaer
I saw a girl do this exact thing for MacOs and decided to adapt it to GNOME. This script switches between wallpapers according to your system's timezone. Still images work on stock GNOME. For videos, this project relies on the LiveLockPaper GNOME exstension.

I only tested on my own OS (Fedora 44, GNOME 50), so let me know if you find issues when in any other GNOME based system.

## Requirements
- Python 3.11 or later
- `ffmpeg` with H.264 decoder (default ffmpeg can't decode H.264 wich is what most .mp4 use, without it script shows `Unable to create decoder` error)
- GNOME with systemd user services
- Optional (for video wallpaper), [LiveLockPaper](https://github.com/DeLuca21/LiveLockPaper)

## Installing
Clone the repo and run these from project folder

```bash
chmod +x timeofday/bin/timeofday
mkdir -p ~/.local/bin ~/.config/timeofday ~/.config/systemd/user
ln -sf "$PWD/timeofday/bin/timeofday" ~/.local/bin/timeofday
```

```bash
cp timeofday/systemd/timeofday.service timeofday/systemd/timeofday.timer ~/.config/systemd/user/
systemctl --user daemon-reload
```

### Extras for Video Wallpaper
Install decoders and LiveLockPaper extension

```bash
sudo dnf install gstreamer1-plugin-libav libavcodec-freeworld mpv
git clone https://github.com/DeLuca21/LiveLockPaper.git ~/LiveLockPaper
mkdir -p ~/.local/share/gnome-shell/extensions
cp -r ~/LiveLockPaper ~/.local/share/gnome-shell/extensions/live-lockpaper@DeLuca21
```

Then log out and back in so that GNOME can find the new extension. After that enable it and open its preferences;

```bash
gnome-extensions enable live-lockpaper@DeLuca21
gnome-extensions prefs live-lockpaper@DeLuca21
```

In the preferences window, add your videos/ in **Library** and enable video wallpaper on **Settings**

## Configuring

Here's where you customize the whole thing, clips and time ranges. I made it using [these clips from the Life Is Strange menu](https://www.youtube.com/watch?v=THqyCADVzzw&list=RDTHqyCADVzzw&start_radio=1). But you can modify this file to your liking

```toml
[slots]
dawn = 6
morning = 9
evening = 15
dusk = 18
night = 21

[clips]
dawn = "~/porject/lis-fed/videos/bg1.mp4"
morning = "~/porject/lis-fed/videos/bg4.mp4"
evening = "~/porject/lis-fed/videos/bg3.mp4"
dusk = "~/porject/lis-fed/videos/bg5.mp4"
night = "~/porject/lis-fed/videos/bg2.mp4"
```

You can be more organized than me when naming the files...

## Tests
### Run Manually
```bash
timeofday dawn #applies a specific slot
timeofday #applies the slot of the current time
```

exoected output:

```
Video: switched (or "Video: unchanged" in case you tried to put on the wallpaper that was already on. Also "LiveLockPaper not found; still image applied only" In case you're not doing live videos.)

Applied: dawn (or whatever slot you used)
```

### Run Automatically

```bash
systemctl --user enable --now timeofday.timer
systemctl --user list-timers | grep timeofday #shows the next run. Timer runs 10 seconds after login an then every 5 minutes after that
```
