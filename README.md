# MusicBrainz Ratings Helper

Push Navidrome ratings to MusicBrainz.

The helper reads ratings from Navidrome through the Subsonic API and submits them to MusicBrainz as:

- artist ratings -> MusicBrainz artists
- album ratings -> MusicBrainz release groups
- song ratings -> MusicBrainz recordings

For songs, release-group expansion is enabled by default. When a rated Navidrome song belongs to a MusicBrainz release group, the helper finds matching recordings in that group so alternate releases can receive the same recording rating.

## Setup

Install dependencies:

```bash
python3 -m pip install -r requirements.txt
```

Using a Linux virtual environment is recommended:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Create a `.env` or set these environment variables:

```text
NAVIDROME_BASE_URL=https://navidrome.example.com
NAVIDROME_USERNAME=your-navidrome-username
NAVIDROME_PASSWORD=your-navidrome-password

MB_USERNAME=your-musicbrainz-username
MB_PASSWORD=your-musicbrainz-password
```

The helper automatically reads `.env` from the current project directory.

## Usage

Preview what would be submitted:

```bash
python musicbrainz-ratings-helper.py --dry-run
```

Run for one Navidrome artist:

```bash
python musicbrainz-ratings-helper.py --dry-run --artist-id 5YPCM8WgUTYDxJPS8QUuOO
```

Resume from a Navidrome artist and continue onward in artist order:

```bash
python musicbrainz-ratings-helper.py --start-artist-id 7dB07x8Q2P9jPvGeDHxIFa
```

Resume from a Navidrome album and continue onward in album-title order:

```bash
python musicbrainz-ratings-helper.py --start-album-id 5WYUQdSkSzSHf4714jsHDM
```

Submit ratings for real:

```bash
python musicbrainz-ratings-helper.py
```

## Useful Flags

- `--dry-run` previews the same rating batches without posting to MusicBrainz.
- `--artist-id ID` limits album and song processing to one Navidrome artist.
- `--start-artist-id ID` skips artists until the matching Navidrome artist, then continues onward in artist order. Album/song processing also follows artist order when this flag is used.
- `--start-album-id ID` skips album/song processing until the matching Navidrome album, then continues onward in the current album traversal order. When no `--entity` flags are provided, this resumes only `song` and `album` work. Skips all artists ratings. 
- `--entity song`, `--entity album`, and `--entity artist` limit exported entity types. Repeat the flag for multiple types.
- `--max-artists N` limits artist rating collection.
- `--max-albums N` limits album/song collection.
- `--no-expand-release-groups` disables recording fan-out within release groups.
- `--log-level DEBUG` shows detailed matching and MusicBrainz resolution logs.

## Logging

Normal logs are grouped by artist and album. Rating lines show the Navidrome source rating and the MusicBrainz rating that will be submitted:

```text
Artist: s:2 -> mb:40 | 3Breezy / 3Breezy: dry-run
Album: s:3 -> mb:60 | Murda She Wrote / 3Breezy: dry-run
Recording: s:2 -> mb:40 | Bacc To Tha Basics / 3Breezy: dry-run
```

The final summary includes scanned counts and previewed/submitted counts.

Detailed recording ID resolution is logged only with `--log-level DEBUG`.

## Notes

- Navidrome uses 1-5 star ratings. MusicBrainz user ratings use a 0-100 scale, so the helper submits `rating * 20`.
- Zero or missing ratings are skipped.
- MusicBrainz API requests are throttled to about one request per second.
- Rating submissions are batched into one MusicBrainz POST where possible instead of sending one request per rating.
- Navidrome access uses the Subsonic API only; the helper does not read the Navidrome database directly.
