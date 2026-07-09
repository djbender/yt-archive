# yt-archive

A thin `yt-dlp` wrapper for archiving an entire YouTube channel, playlist, or
single video with sane defaults: dated filenames, embedded metadata /
thumbnail / human-made subtitles, and incremental re-runs via a download
archive file.

## Requirements

- [`yt-dlp`](https://github.com/yt-dlp/yt-dlp) on `PATH`

## Install

Symlink `bin/yt-archive` onto your `PATH`, e.g.:

```sh
ln -s "$(pwd)/bin/yt-archive" ~/bin/yt-archive
```

## Usage

```sh
yt-archive "https://www.youtube.com/@ChannelName/videos"
```

Re-running the same command later only fetches new uploads — already
downloaded videos are recorded in a download-archive file and skipped.

### Estimate size before downloading

```sh
yt-archive --size "https://www.youtube.com/@ChannelName/videos"
```

Prints an estimated total download size without downloading anything.
Sizes come from `yt-dlp`'s video metadata: an exact `Content-Length` when
YouTube provides one, otherwise a bitrate × duration estimate. Videos with
no size metadata at all are counted separately and excluded from the total,
so treat the number as an estimate, not a guarantee.

### Confirm before downloading

```sh
yt-archive --confirm "https://www.youtube.com/@ChannelName/videos"
```

Shows the size estimate, then prompts `y/N` before downloading.

### Extra yt-dlp args

Any additional arguments are passed through to `yt-dlp`, placed before the
URL:

```sh
yt-archive "https://www.youtube.com/@ChannelName/videos" --playlist-end 20
yt-archive "https://www.youtube.com/@ChannelName/videos" --dateafter 20240101
yt-archive "https://www.youtube.com/@ChannelName/videos" --cookies-from-browser firefox
```

## Options

| Flag | Description |
|------|-------------|
| `-s`, `--size` | Estimate total download size, download nothing |
| `-c`, `--confirm` | Show size estimate, prompt before downloading |
| `-h`, `--help` | Show usage |

## Environment variables

| Variable | Default | Description |
|----------|---------|--------------|
| `YT_ARCHIVE_FILE` | `archive.txt` | Path to the download-archive file |
| `YT_ARCHIVE_SUB_LANGS` | `en.*` | Subtitle languages to embed |

## What it does

Equivalent to running:

```sh
yt-dlp \
  -o "%(uploader)s/%(upload_date)s - %(title)s [%(id)s].%(ext)s" \
  --download-archive archive.txt \
  -f "bv*+ba/b" \
  --merge-output-format mp4 \
  --embed-metadata \
  --embed-thumbnail \
  --embed-subs \
  --sub-langs "en.*" \
  -i \
  "<url>"
```

Only human-made subtitles are embedded (auto-generated captions are
intentionally excluded).

## License

MIT — see [LICENSE](LICENSE).
