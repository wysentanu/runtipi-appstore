# Overview
Navicord is a headless Discord Rich Presence client for Navidrome with album art and timestamps.

## Important Notes
- Requires a Discord application client ID and Discord account token.
- Polling is frequent; reduce log level if logs grow quickly.

## Configurable Environment Variables
- `DISCORD_CLIENT_ID` - Discord application client ID.
- `DISCORD_TOKEN` - Discord account token.
- `LASTFM_API_KEY` - Last.fm API key for album art.
- `NAVIDROME_SERVER` - Navidrome base URL.
- `NAVIDROME_USERNAME` - Navidrome username.
- `NAVIDROME_PASSWORD` - Navidrome password.
- `ACTIVITY_NAME` - Activity label override (ARTIST, ALBUM, TRACK, or custom).
- `POLLING_TIME` - Polling interval in seconds.

## Links
- [GitHub Repository](https://github.com/logixism/navicord)
