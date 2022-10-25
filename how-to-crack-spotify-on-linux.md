# How to crack Spotify on Linux
It's been a while I stopped paying for a Spotify Premium subscription. $10 a month seems a bit too expensive to play music, in 2022. 

You may have stumbled upon this article by curiosity, but chances are you're here to know what are the best methods to block Spotify ads on Linux. So let's get straight into it. 

## Via a `Progressive Web App`
A progressive web app is an application that's composed of web pages or websites, which can be displayed to the user in the same way as native applications or mobile applications.

Maybe a website once prompted you to 'Add this website to your home screen', meaning it is a PWA.

Anyway, you'll want to install an **ad-blocker**. I recommend using [uBlock Origin](https://ublockorigin.com).

Once that's done, head over to https://open.spotify.com using a **PWA-compatible browser** (`Google Chrome` or `Edge`) and install the app[^1].

And tadaa. You now have a functionnal progressive web app installed on your system with ads removed on Spotify. 

![preview](https://user-images.githubusercontent.com/37367577/197588540-8993b787-029d-4508-8e15-9708adc23057.png)

## Via the `Flatpak`/`.deb` package
We're going to use [this project](https://github.com/abba23/spotify-adblock) to prevent ads from playing on the official `.deb` and `Flatpak` packages.

> Spotify adblocker for Linux (macOS untested) that works by wrapping `getaddrinfo` and `cef_urlrequest_create`. It blocks requests to domains that are not on the allowlist, as well as URLs that are on the denylist.

Please note that this technique **does not work** with the `Snap` Spotify package.

### Installation
0. You will need to have `rust` installed on your machine. 
```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

1. Start by cloning the repository and building it:

```shell 
git clone https://github.com/abba23/spotify-adblock.git
cd spotify-adblock 
make 
```

2. Then, run the following command so that our built program is copied to the correct location:

```shell
sudo make install
```

#### `Flatpak`
If you use **`Flatpak`**, you will need to run the following commands (we're still in the `spotify-adblock` directory). 
These will create necessary directories inside your home directory and override the `com.spotify.Client` package.

```shell
mkdir -p ~/.spotify-adblock && cp target/release/libspotifyadblock.so ~/.spotify-adblock/spotify-adblock.so
mkdir -p ~/.config/spotify-adblock && cp config.toml ~/.config/spotify-adblock
flatpak override --user --filesystem="~/.spotify-adblock/spotify-adblock.so" --filesystem="~/.config/spotify-adblock/config.toml" com.spotify.Client
```

### Usage
We've now successfully installed `spotify-adblock`. 

Let's launch our 'cracked' Spotify from the command line. 

#### `.deb`
```shell
LD_PRELOAD=/usr/local/lib/spotify-adblock.so spotify
```

#### `Flatpak`
```shell
flatpak run --command=sh com.spotify.Client -c 'eval "$(sed s#LD_PRELOAD=#LD_PRELOAD=$HOME/.spotify-adblock/spotify-adblock.so:#g /app/bin/spotify)"'
```

### Creating a `.desktop` file 
So that you can launch Spotify from the 'Activities' menu. 

#### `.deb`
```markdown 
[Desktop Entry]
Type=Application
Name=Spotify (adblock)
GenericName=Music Player
Icon=spotify-client
TryExec=spotify
Exec=env LD_PRELOAD=/usr/local/lib/spotify-adblock.so spotify %U
Terminal=false
MimeType=x-scheme-handler/spotify;
Categories=Audio;Music;Player;AudioVideo;
StartupWMClass=spotify
```

#### `Flatpak`
```markdown
[Desktop Entry]
Type=Application
Name=Spotify (adblock)
GenericName=Music Player
Icon=com.spotify.Client
Exec=flatpak run --file-forwarding --command=sh com.spotify.Client -c 'eval "$(sed s#LD_PRELOAD=#LD_PRELOAD=$HOME/.spotify-adblock/spotify-adblock.so:#g /app/bin/spotify)"' @@u %U @@
Terminal=false
MimeType=x-scheme-handler/spotify;
Categories=Audio;Music;Player;AudioVideo;
StartupWMClass=spotify
```

![preview](https://user-images.githubusercontent.com/37367577/197834698-a28b45fe-1937-4674-84aa-0bbca951a123.png)

## Final words
These were the two best solutions I've found to block ads on Spotify for Linux. 

I hope you found this article useful. If that's the case, you can share it with people you think will be interested.

And I'll catch you in the next one.

[^1]: You'll find help for Chrome [here](https://support.google.com/chrome/answer/9658361?hl=en&co=GENIE.Platform%3DDesktop) and for Edge [here](https://howtomanagedevices.com/windows-10/5720/how-to-install-progressive-web-app-in-microsoft-edge-chromium/)