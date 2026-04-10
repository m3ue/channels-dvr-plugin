# Channels DVR Plugin

An official plugin for [m3u-editor](https://github.com/m3ue/m3u-editor) that maps Gracenote station IDs from a local [Channels DVR](https://getchannels.com/) install to your playlist channels via the DVR's `/dvr/guide/stations` API.

## Features

- Fetches all guide stations from your Channels DVR server in a single request
- Matches channels by call sign first, then by station name, for reliable resolution
- Strips common quality suffixes (`HD`, `SD`, `FHD`, `UHD`, `4K`) before retrying unmatched channels
- Normalised, punctuation-insensitive matching (case-insensitive, strips non-alphanumeric)
- Supports multi-provider DVR setups (stations from all providers are indexed)
- Dry-run support on all actions

## Requirements

- m3u-editor with plugin support enabled
- A running Channels DVR server accessible from the m3u-editor host
- `network_egress` permission

## Installation

Install via the m3u-editor Plugins page using the latest GitHub release, or via Artisan:

```bash
php artisan plugins:stage-github-release \
  https://github.com/m3ue/channels-dvr-plugin/releases/download/v1.0.0/channels-dvr-v1.0.0.zip \
  --sha256=<checksum>
```

Once staged, approve the install review in the UI and enable the plugin.

## Settings

| Setting | Default | Description |
|---|---|---|
| `dvr_host` | — | IP or hostname of your Channels DVR server (without port), e.g. `192.168.1.100` or `my-dvr.local`. |
| `dvr_port` | `8089` | Channels DVR web UI port. |
| `default_playlist_id` | — | Playlists to sync station IDs automatically after each playlist sync. |
| `overwrite_existing` | `false` | Overwrite channels that already have a station ID. |
| `skip_vod` | `true` | Skip VOD channels. |

## Actions

| Action | Description |
|---|---|
| `health_check` | Connects to Channels DVR and reports the number of stations found. Dry-run safe. |
| `sync_station_ids` | Syncs station IDs for a selected playlist. |

## Hook

Subscribes to `playlist.synced`. When a playlist sync completes, the plugin automatically syncs station IDs for any playlists configured in `default_playlist_id`.

## How Matching Works

The plugin fetches all stations from `/dvr/guide/stations`, builds a normalised lookup (call sign → station ID, then name → station ID), and walks each enabled channel in the playlist. Matching is attempted in order:

1. Exact normalised match on the channel display name
2. Strip trailing quality suffix (`HD`, `FHD`, `UHD`, `4K`, `1080p`, etc.) and retry

## License

MIT
