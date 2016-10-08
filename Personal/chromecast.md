# Chromecast

### usage

```

// start playback of a local video file
castnow ./myvideo.mp4

// start playback of video and mp3 files in the local directory
castnow ./mydirectory/

// playback 3 videos after each other
castnow video1.mp4 video2.mp4 video3.mp4

// start playback of an mp4 file over the web
castnow http://commondatastorage.googleapis.com/gtv-videos-bucket/ED_1280.mp4

// start playback of a video over torrent
castnow <url-to-torrent-file OR magnet>

// start playback of a video over torrent with local subtitles
castnow <url-to-torrent-file OR magnet> --subtitles </local/path/to/subtitles.srt>

// transcode some other video format to mp4 while playback (requires ffmpeg)
castnow ./myvideo.avi --tomp4

// re-attach to a currently running playback session
castnow

```

### options

* `--tomp4` Transcode a video file to mp4 during playback. This option requires
ffmpeg to be installed on your computer. The play / pause controls are currently
not supported in transcode mode.

* `--device "my chromecast"` If you have more than one Chromecast on your network,
use the `--device` option to specify the device on which you want to start casting.
Otherwise, castnow will just use the first device it finds in the network.

* `--address 192.168.1.4` The IP address or hostname of your chromecast. This will skip
the MDNS scan.

* `--subtitles <path/URL>` This can be a path or URL to a vtt or srt file that
contains subtitles.

* `--subtitle-scale 1.5` Scaling factor for the size of the subtitle font. Default is 1.0.

* `--subtitle-color #FFFFFFFF` Foreground RGBA color of the subtitle font.

* `--myip 192.168.1.8` Your main IP address \(useful if you have multiple network adapters)

* `--quiet` Hide the player timeline.

* `--peerflix-<option> <argument>` Pass options to peerflix.

* `--ffmpeg-<option> <argument>` Pass options to ffmpeg.

* `--type <type>` Explicity set the mime-type of the first item in the playlist (e.g. 'video/mp4').

* `--seek <hh:mm:ss>` Seek to the specified time on start using the format hh:mm:ss or mm:ss.

* `--bypass-srt-encoding` Disable automatic UTF-8 encoding of SRT subtitles.

* `--loop` Play the list of files over and over in a loop, forever.

* `--help` Display help message.

### player controls

```

space   // toggle between play and pause
m       // toggle mute
up      // volume up
down    // volume down
left    // seek backward (keep pressed / multiple press for faster seek)
right   // seek forward (keep pressed / multiple press for faster seek)
n       // next item in the playlist (only supported in launch-mode)
s       // stop playback
q       // quit

```

### YouTube support

We had to drop direct YouTube support for now since google changed the chromecast YouTube API.
However, there is a nice workaround in combination with the tool [youtube-dl](https://github.com/rg3/youtube-dl):

`youtube-dl -o - https://youtu.be/BaW_jenozKc | castnow --quiet -`



### installation

`npm install castnow -g`
