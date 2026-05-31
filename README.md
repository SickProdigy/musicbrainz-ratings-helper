# Navidrome to MusicBrainz Ratings Push

This script reads ratings from Navidrome over its Subsonic API and submits them to MusicBrainz.

It supports:

- song ratings -> MusicBrainz recordings
- album ratings -> MusicBrainz release groups
- artist ratings -> MusicBrainz artists

For songs, it will also expand a release-group match and rate every matching recording it can find in that group instead of stopping at the first one.

## Setup

1. Install dependencies:

   `python -m pip install -r requirements.txt`

Optional: run inside a Python virtual environment (recommended)

- Windows (PowerShell):

   ```powershell
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1
   python -m pip install -r requirements.txt
   ```

- Windows (cmd.exe):

   ```cmd
   python -m venv .venv
   .\.venv\Scripts\activate.bat
   python -m pip install -r requirements.txt
   ```

- Unix/macOS:

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   python -m pip install -r requirements.txt
   ```

2. Set your Navidrome and MusicBrainz credentials:

   - `NAVIDROME_BASE_URL`
   - `NAVIDROME_USERNAME`
   - `NAVIDROME_PASSWORD`

   - `MB_USERNAME`
   - `MB_PASSWORD`

3. Run the script:

   `python musicbrainz-ratings-helper.py`

## Useful flags

- `--navidrome-base-url`, `--navidrome-username`, and `--navidrome-password` to override environment variables
- `--entity song --entity album --entity artist` to limit what gets exported
- `--dry-run` to preview submissions
- `--max-targets 1` to limit a dry run to the first resolved target
- `--no-expand-release-groups` to disable release-group fan-out for songs

## Notes

- The script submits one through five star ratings and converts them to MusicBrainz's 0-100 scale.
- Zero ratings are skipped.
- MusicBrainz requires authenticated XML POST submissions and a meaningful client string.
- Navidrome access uses the Subsonic API only; the script does not read the database directly.