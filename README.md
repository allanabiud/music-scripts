# Music Management Scripts & Tools

This directory contains automation utilities for managing, tagging, and organizing the music collection.

---

## 1. `organize_music.py`

Organizes unsorted audio files into the library following **MusicBrainz Picard Preset 2** conventions, enhanced with **online metadata scraping** and **exact 1200x1200px cover art handling**.

### Generated Structure
```text
~/Music/Library/Music/<Album Artist>/[<Year>] <Album>/<Track Number> - <Title>.mp3
~/Music/Library/Music/<Album Artist>/[<Year>] <Album>/cover.jpg
```

### Key Capabilities

1. **Online Metadata Sourcing (iTunes & Deezer Fallback):**
   * Automatically queries Apple Music / iTunes and Deezer when tracks lack matching info on MusicBrainz.
   * Fills official Title, Lead Artist, Single/Album name, exact Release Date/Year, Track numbers (`1/1`, `3/21`), and Genre (`TCON`).
   * Can be toggled off with `--no-online`.

2. **Highest Quality Cover Art (Exact 1200x1200px):**
   * Downloads master artwork from Apple CDN dynamically formatted to **1200x1200px**.
   * Rejects images lower than 1200px (matching Picard's rule).
   * Automatically normalizes any local images to **1200x1200px** using Lanczos resampling.
   * Both saves `cover.jpg` in the album folder and embeds the 1200x1200px image directly into the file's `APIC` ID3 tag.

3. **Strict ID3v2.4 & No Legacy ID3v1:**
   * Always saves files strictly as **ID3v2.4.0** (UTF-8).
   * Completely strips any legacy **ID3v1** tags (`v1=0`).

4. **Picard Rules Integration:**
   * **Remove Junk Tags:** Purges downloader comments (`COMM`), descriptions, container artifacts (`major_brand`), encoder tags (`TSSE`, `TENC`), DJ/BPM tags (`TBPM`, `TKEY`), and non-essential MB IDs.
   * **One Album Artist:** Extracts strictly the primary album artist for `TPE2` (e.g. `Avicii` from `Avicii, Sandro Cavazza`), while preserving legitimate bands (`Various Artists`, `Mumford & Sons`, `Nico & Vinz`, etc.).
   * **Dynamic Library Mapping:** Resolves artist names against existing Library folders using NFKC normalization to match Picard's Unicode hyphens (e.g. `K‐391`), curly quotes, and exact casing.

---

### Usage

#### Dry Run (Preview matching & paths without changing files):
```bash
python3 ~/Music/Scripts/organize_music.py --source ~/Music/TO-SORT --dry-run
```

#### Execute Reorganization:
```bash
python3 ~/Music/Scripts/organize_music.py --source ~/Music/TO-SORT --execute
```

#### CLI Options
| Flag | Description | Default |
| :--- | :--- | :--- |
| `-s`, `--source` | Path to directory containing unsorted tracks | `~/Music/TO-SORT` |
| `-l`, `--library` | Target library directory | `~/Music/Library/Music` |
| `--dry-run` | Preview changes without modifying files | `True` (default if no flags) |
| `--execute` | Apply changes, move files, embed 1200px art | `False` |
| `--no-online` | Skip online metadata & cover art scraping | `False` |
