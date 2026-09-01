# Third-party licences and the obligations they carry

Playhead ships three third-party libraries. Two are permissive and need only
attribution. One is LGPL and carries **ongoing obligations that must be met on
every single release**. Read this before shipping.

## Summary

| Library | Licence | Linking | Obligation |
|---|---|---|---|
| MediaInfoLib | BSD-2-Clause | static | attribution only |
| ZenLib | Zlib | static | attribution only |
| **FFmpeg (libavformat, libavcodec parsers)** | **LGPL-2.1+** | **static** | **attribution + source + object files, every release** |

## Why FFmpeg is here

AVFoundation cannot open Matroska at all (measured: `-11828 Cannot Open`), so it
cannot produce thumbnails for MKV, the format that most distinguishes Playhead
from an AVFoundation-only inspector. FFmpeg is used **only to demux**. Frames are
decoded by Apple's VideoToolbox, which keeps the binary small and keeps codec
patent licensing on the platform rather than on us.

## The LGPL obligation, in full

LGPL-2.1 allows use in a closed-source app through one of two routes. Dynamic
linking is the usual one, but iOS forces static linking, so Playhead relies on
the second: **section 6(b), object-code relinking**. A user must be able to
modify FFmpeg and relink it into the app.

Every release must therefore publish, on the docs site, all of:

1. **The exact FFmpeg source** used for that build, not a link to upstream HEAD.
   It must correspond precisely to the shipped binaries.
2. **Playhead's object files** (`.o`/`.a`) for each platform, so a user can relink
   the app against their own FFmpeg build. Object files are not source code; the
   app's own source is not disclosed by this.
3. **The build script and configure flags** used, so the build is reproducible.

And the build itself must:

4. Be configured **without `--enable-gpl` and without `--enable-nonfree`**.
   Turning either on makes the whole app GPL and this arrangement collapses.
5. Not rename or obfuscate the FFmpeg libraries.

And the app must:

6. Credit FFmpeg in an in-app acknowledgements screen, naming the licence and
   linking to the source download.
7. Reference FFmpeg in the EULA or terms, stating we do not own it.

## Release checklist item

Add to the release procedure, and do not skip it when only the app changed:

- [ ] FFmpeg source archive for this exact build uploaded and linked
- [ ] Object files for this exact build uploaded and linked
- [ ] Acknowledgements screen lists MediaInfoLib (BSD-2), ZenLib (Zlib), FFmpeg (LGPL-2.1) with a working source link
- [ ] `configure` flags contain neither `--enable-gpl` nor `--enable-nonfree`

Generate the release materials from the exact archive inputs, never from a
later development build:

```bash
scripts/package-lgpl-materials.sh source --output release-materials/<version>/source
scripts/package-lgpl-materials.sh objects --platform macos --derived-data <macos-derived-data> --output release-materials/<version>/macos-objects
scripts/package-lgpl-materials.sh objects --platform ios --derived-data <ios-derived-data> --output release-materials/<version>/ios-objects
```

Each package includes a manifest. Keep its SHA-256 values with the release
record and publish the source archive and both object packages before the
corresponding App Store build is submitted.

## If this ever becomes inconvenient

The fallback is to drop FFmpeg and lose MKV thumbnails only. Metadata for MKV
comes from MediaInfoLib and is unaffected, so the product still works. Do not
respond to the inconvenience by static-linking without publishing object files:
that is the one configuration that is an actual licence violation.

## References

- FFmpeg legal: https://www.ffmpeg.org/legal.html
- LGPL-2.1 section 6: https://www.gnu.org/licenses/old-licenses/lgpl-2.1.html
