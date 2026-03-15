
# 📸 QQ Zone Photos Manager (QQ空间相册管理器)

A CLI tool for managing QQ Zone (QQ空间) photo albums — list albums, browse photos, upload, download, and create albums, all from the command line.

## Features

- 🔐 **QR Code Login** — Scan with QQ mobile app, no password needed
- 📂 **List Albums** — View all photo albums in a QQ Zone
- 🖼️ **Browse Photos** — List all photos within an album
- ⬆️ **Upload Photos** — Upload images to a specific album or the default album
- ⬇️ **Download Photos** — Download a single photo or an entire album in batch
- ➕ **Create Albums** — Create new photo albums with custom title and description

## Requirements

- Python 3.8+
- Dependencies: `requests`, `Pillow`, `qrcode`, `pycryptodome`

## Installation

```bash
# Clone the repository
git clone https://github.com/your-username/qq-zone-photo.git
cd qq-zone-photo

# Create a virtual environment and install dependencies
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Authentication

There are two ways to authenticate:

### Option 1: QR Code Login (Recommended)

```bash
python3 scripts/qzone_photos.py --action login --cookies cookies.json
```

This will:
1. Display a QR code in the terminal and open it in the system image viewer (macOS Preview)
2. Wait for you to scan the QR code with your QQ mobile app
3. Save session cookies to `cookies.json` upon successful login

### Option 2: Manual Cookie Configuration

If you already have cookies from a browser session, create a `cookies.json` file manually:

```json
{
  "qq_number": "123456789",
  "p_skey": "your_p_skey_value",
  "skey": "your_skey_value",
  "uin": "o0123456789"
}
```

> **How to get cookies:** Open [QQ Zone](https://user.qzone.qq.com/) in your browser → F12 → Application → Cookies → copy `p_skey`, `skey`, and `uin` values.

## Usage

All commands use the `scripts/qzone_photos.py` script. The `--cookies` flag defaults to `cookies.json` in the project root.

### List Albums

```bash
python3 scripts/qzone_photos.py --action list \
  --cookies cookies.json
```

Output example:
```json
[
  {
    "id": "V13dJ8rf0s7Rgc",
    "name": "说说和日志相册",
    "photo_count": 5
  }
]
```

### List Photos in an Album

```bash
python3 scripts/qzone_photos.py --action photos \
  --album-id "ALBUM_ID" \
  --cookies cookies.json
```

### Upload a Photo

```bash
python3 scripts/qzone_photos.py --action upload \
  --photo /path/to/image.jpg \
  --cookies cookies.json
```

Upload to a specific album:

```bash
python3 scripts/qzone_photos.py --action upload \
  --photo /path/to/image.jpg \
  --album-id "ALBUM_ID" \
  --cookies cookies.json
```

### Download a Single Photo

```bash
python3 scripts/qzone_photos.py --action download \
  --url "PHOTO_URL" \
  --output ./downloads \
  --cookies cookies.json
```

### Download All Photos from an Album

```bash
python3 scripts/qzone_photos.py --action download-album \
  --album-id "ALBUM_ID" \
  --output ./downloads \
  --cookies cookies.json
```

### Create a New Album

```bash
python3 scripts/qzone_photos.py --action create \
  --title "My Vacation Photos" \
  --desc "Photos from summer 2025" \
  --cookies cookies.json
```

## CLI Reference

| Argument | Description |
|----------|-------------|
| `--action` | **Required.** One of: `login`, `list`, `photos`, `download`, `download-album`, `upload`, `create` |
| `--qq` | QQ number (auto-detected from cookies if omitted) |
| `--cookies` | Path to cookies JSON file (default: `cookies.json`) |
| `--album-id` | Album ID (required for `photos`, `download-album`; optional for `upload`) |
| `--photo` | Path to image file (required for `upload`) |
| `--url` | Photo URL (required for `download`) |
| `--output` | Output directory for downloads (default: `./downloads`) |
| `--title` | Album title (required for `create`) |
| `--desc` | Album description (optional, for `create`) |

## Quick Start Example

```bash
# 1. Install
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# 2. Login via QR code
python3 scripts/qzone_photos.py --action login

# 3. List your albums
python3 scripts/qzone_photos.py --action list

# 4. Upload a photo
python3 scripts/qzone_photos.py --action upload --photo ~/Pictures/photo.jpg

# 5. Download all photos from an album
python3 scripts/qzone_photos.py --action download-album --album-id "V13dJ8rf0s7Rgc" --output ./backup
```

## Project Structure

```
qq-zone-photo/
├── README.md              # This file
├── SKILL.md               # OpenClaw skill definition
├── _meta.json             # Skill metadata
├── requirements.txt       # Python dependencies
├── cookies.json           # Session cookies (generated after login, git-ignored)
└── scripts/
    └── qzone_photos.py    # Main CLI script
```

## Privacy & Security

- 🔒 Cookies and credentials are stored **locally only** — never uploaded or shared
- 🚫 Never share your `cookies.json` — it grants full access to your QQ Zone account
- ⏰ Session cookies expire periodically; re-login when you see authentication errors
- ⚠️ This tool uses unofficial QQ Zone APIs and may break if Tencent changes their endpoints

## License

MIT
