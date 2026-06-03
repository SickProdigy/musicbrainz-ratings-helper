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

Submit ratings for real:

```bash
python musicbrainz-ratings-helper.py
```

## Useful Flags

- `--dry-run` previews the same rating batches without posting to MusicBrainz.
- `--artist-id ID` limits album and song processing to one Navidrome artist.
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
