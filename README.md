# ShareY
Small command-line utility to easily take screenshots, upload them to a hoster and copy the URL to the clipboard.

The name is a nod to [ShareX](https://github.com/ShareX/ShareX), a great utility which I used when still running
Windows. Since switching to Linux I dearly missed similar functionality, having become so comfortable with my workflow
in ShareX (Hit Alt+X -> Screenshot a region of the screen -> Upload to [imgur](http://imgur.com/) -> Copy URL to
clipboard -> Notify user), that using it quickly became second nature to share or note information visually.

Of course this utility only supports a very small part of ShareX's features, however they are the features I used and
enjoyed most.

## Installation
I made PKGBUILDs for Arch Linux [here](https://github.com/cryzed/PKGBUILDs). You'll have to manually install
[python-imgurpython-git](https://github.com/cryzed/PKGBUILDs/tree/master/python-imgurpython-git) (use `--asdeps`) and
then [sharey](https://github.com/cryzed/PKGBUILDs/tree/master/sharey). As noted in the
[readme](https://github.com/cryzed/PKGBUILDs/blob/master/README.md), feel free to adopt any of these packages and upload
them to the AUR. I don't really want to maintain them.


## Usage
```
usage: sharey [-h] [--include-cursor] [--include-window-decorations]
              [--convert-bigger-than SIZE] [--jpeg-quality QUALITY]
              [--config PATH] [--uploader NAME]
              [{FullScreen,CurrentScreen,RectangularRegion,ActiveWindow,WindowUnderCursor}]

positional arguments:
  {FullScreen,CurrentScreen,RectangularRegion,ActiveWindow,WindowUnderCursor}

optional arguments:
  -h, --help            show this help message and exit
  --include-cursor
  --include-window-decorations
  --convert-bigger-than SIZE
                        Size in KiB
  --jpeg-quality QUALITY
                        1-100%
  --config PATH
  --uploader NAME       Available uploaders: imgur.com
```

Currently you have to manually create a file in `~/.config/sharey.ini` with the following contents to upload screenshots
to imgur.
```
[imgur.com]
client_id = <your imgur client secret>
client_secret = <your imgur client secret>
```

You can get the client id and secret by [registering an application](https://api.imgur.com/oauth2/addclient) with imgur,
just choose "Anonymous usage without user authorization", use "http://127.0.0.1/" for the authorization callback URL and
fill in the rest of the information. This step should actually be done by the script itself, however I haven't found
a good way to do so easily in Python without firing up a `SimpleHTTPRequestHandler` and adding much extra code
specifically dealing with the OAuth2 mechanics (maybe someone can recommend something?).

Personally I defined two custom shortcuts in KDE:

* `sharey --include-cursor --convert-bigger-than 768` triggered on Alt+X
* `sharey ActiveWindow --include-cursor --include-window-decorations --convert-bigger-than 768` triggered on Alt+C

This allows me to quickly take screenshots of a region on the screen or an entire window, including its decoration by
the window manager. When the resulting PNG screenshot exceeds 768 kilobytes, it is automatically converted to JPEG and
uploaded instead.

## Dependencies
* Python 3
* Spectacle
* pydbus
* Pillow
* imgurpython
* xsel
* pyperclip

The script is written in Python 3, Spectacle is used as the primary (and currently only) backend for taking screenshots,
pydbus is used to communicate with Spectacle via D-Bus to take screenshots, Pillow to optionally convert screenshots to
JPEG after exceeding a certain size as PNG, imgurpython to upload the image via imgur's API, xsel as a reliable
clipboard backend for pyperclip to copy the URL to the clipboard in a (hopefully) desktop-environment-agnostic fashion.
