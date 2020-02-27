<h1 align="center">
  <div>
    <img src="https://raw.githubusercontent.com/adrg/adrg.github.io/master/assets/projects/libvlc-go/libvlc-go-logo.jpg" width="150px" alt="libvlc-go logo"/>
  </div>
  libvlc-go
</h1>

<h3 align="center">Go bindings for libVLC version 2.X/3.X/4.X.</h3>

<p align="center">
    <a href="https://godoc.org/github.com/adrg/libvlc-go" rel="nofollow">
        <img src="https://img.shields.io/badge/godoc-reference-blue.svg?style=flat-square" alt="GoDoc" />
    </a>
    <a href="https://opensource.org/licenses/MIT" rel="nofollow">
        <img src="https://img.shields.io/badge/license-MIT-red.svg?style=flat-square" alt="License: MIT" />
    </a>
    <a href="https://goreportcard.com/report/github.com/adrg/libvlc-go" rel="nofollow">
        <img src="https://goreportcard.com/badge/github.com/adrg/libvlc-go" alt="Go Report Card" />
    </a>
</p>

The package can be useful for adding multimedia capabilities to applications through the provided player
interfaces.  
Full documentation can be found at: https://godoc.org/github.com/adrg/libvlc-go

## Prerequisites

The libVLC development files are required. Instructions for installing the
VLC SDK on multiple operating systems can be found on the wiki pages of this project.

- [Install on Linux](https://github.com/adrg/libvlc-go/wiki/Install-on-Linux)
- [Install on Windows](https://github.com/adrg/libvlc-go/wiki/Install-on-Windows)

## Installation
```
go get github.com/adrg/libvlc-go
```

Build for libVLC < v3.0.0.

```
go build -tags legacy
```

## Usage

```go
package main

import (
    "log"

    vlc "github.com/adrg/libvlc-go"
)

func main() {
    // Initialize libVLC. Additional command line arguments can be passed in
    // to libVLC by specifying them in the Init function.
    if err := vlc.Init("--no-video", "--quiet"); err != nil {
        log.Fatal(err)
    }
    defer vlc.Release()

    // Create a new player.
    player, err := vlc.NewPlayer()
    if err != nil {
        log.Fatal(err)
    }
    defer func() {
        player.Stop()
        player.Release()
    }()

    // Add a media file from path or from URL.
    // Set player media from path:
    // media, err := player.LoadMediaFromPath("localpath/test.mp4")
    // Set player media from URL:
    media, err := player.LoadMediaFromURL("http://stream-uk1.radioparadise.com/mp3-32")
    if err != nil {
        log.Fatal(err)
    }
    defer media.Release()

    // Start playing the media.
    err = player.Play()
    if err != nil {
        log.Fatal(err)
    }

    // Retrieve player event manager.
    manager, err := player.EventManager()
    if err != nil {
        log.Fatal(err)
    }

    // Register the media end reached event with the event manager.
    quit := make(chan struct{})
    eventCallback := func(event vlc.Event, userData interface{}) {
        close(quit)
    }

    eventID, err := manager.Attach(vlc.MediaPlayerEndReached, eventCallback, nil)
    if err != nil {
        log.Fatal(err)
    }
    defer manager.Detach(eventID)

    <-quit
}
```

## Examples

* [Player usage](https://github.com/adrg/libvlc-go/blob/master/examples/player.go)
* [List player usage](https://github.com/adrg/libvlc-go/blob/master/examples/list_player.go)
* [Handling events](https://github.com/adrg/libvlc-go/blob/master/examples/event_handling.go)
* [Media information](https://github.com/adrg/libvlc-go/blob/master/examples/media_information.go)

## Stargazers over time

[![Stargazers over time](https://starchart.cc/adrg/libvlc-go.svg)](https://starchart.cc/adrg/libvlc-go)

## Contributing

Contributions in the form of pull requests, issues or just general feedback,
are always welcome.
See [CONTRIBUTING.MD](https://github.com/adrg/libvlc-go/blob/master/CONTRIBUTING.md).

**Contributors**:
[adrg](https://github.com/adrg),
[fenimore](https://github.com/fenimore),
[tarrsalah](https://github.com/tarrsalah),
[danielpellon](https://github.com/danielpellon),
[patknight](https://github.com/patknight),
[sndnvaps](https://github.com/sndnvaps).

## References
For more information see the
[libVLC](https://www.videolan.org/developers/vlc/doc/doxygen/html/group__libvlc.html) documentation.

## License
Copyright (c) 2018 Adrian-George Bostan.

This project is licensed under the [MIT license](https://opensource.org/licenses/MIT).
See [LICENSE](https://github.com/adrg/libvlc-go/blob/master/LICENSE) for more details.
