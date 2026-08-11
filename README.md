# Playhead

A media inspector for macOS and iOS. It reads the files QuickTime will not open,
and then says what any of it means.

Site: https://corvusdevs.github.io/Playhead/

## What it does

- Reads Matroska, MP4, QuickTime, WebM, AVI, MPEG-TS, Ogg and more, plus still
  images with their EXIF, IPTC and GPS.
- Lays every track on one clock, so a subtitle that covers ninety seconds looks
  different from one that runs the whole film.
- Reports HDR in the words people use, with the mastering brightness spelled out.
- Asks *your* Mac whether it can decode *this* file in hardware, rather than
  looking the answer up in a table of rules.
- Compares two files, exports six ways, and copies any field or a whole report.

## Building

    xcodegen generate
    xcodebuild -project Playhead.xcodeproj -scheme Playhead build
    cd PlayheadKit && swift test

Engineering notes, specs and plans live in `engineering/`. The public site lives
in `docs/`, which is the only directory intended to be published.

## Open-source licences

Playhead bundles libmediainfo (BSD-2), ZenLib (Zlib) and a demux-only build of
FFmpeg (LGPL-2.1). See `docs/LICENSING.md` for the obligations that come with
shipping them, including the object files that must accompany each release.
