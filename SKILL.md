---
name: qq-zone-photo
description: Manage QQ Zone (QQ空间) photo albums. List albums, download photos, upload photos and create albums. Use when the user wants to backup, organize or manage images in their QQ Zone photo library.
metadata: {"openclaw":{"emoji":"📸","requires":{"auth":["qq-cookies"]}}}
---

# QQ Zone Photos Manager (QQ空间相册管理器)

This skill provides a way to interact with QQ Zone (QQ空间) photo albums to automate photo management tasks including listing albums, downloading photos, uploading photos, and creating new albums.

## Setup

1. **Get QQ Cookies**: Log in to [QQ Zone](https://user.qzone.qq.com/) in your browser and export your cookies.
2. **Cookie File**: Save your cookies in a JSON file (see format below).
3. **Environment**: This skill uses a Python virtual environment located in its folder.

### Cookie File Format

Create a `cookies.json` file with the following structure:

```json
{
  "qq_number": "123456789",
  "p_skey": "your_p_skey_value",
  "skey": "your_skey_value",
  "uin": "o0123456789"
}
```

You can obtain these values from your browser's developer tools (F12 → Application → Cookies) after logging into QQ Zone.

### Alternative: QR Code Login

You can also use the built-in QR code login flow:

```bash
./scripts/qzone_photos.py --action login --cookies /path/to/cookies.json
```

This will display a QR code in the terminal. Scan it with your QQ mobile app to authenticate.

## Usage

All commands are run through the `scripts/qzone_photos.py` script.

### Login via QR Code
```bash
./scripts/qzone_photos.py --action login --cookies /path/to/cookies.json
```

### List Albums
Useful for finding the ID of an existing album.
```bash
./scripts/qzone_photos.py --action list --qq 123456789 --cookies /path/to/cookies.json
```

### List Photos in an Album
```bash
./scripts/qzone_photos.py --action photos --qq 123456789 --album-id "ALBUM_ID" --cookies /path/to/cookies.json
```

### Download a Photo
```bash
./scripts/qzone_photos.py --action download --url "PHOTO_URL" --output /path/to/save/ --cookies /path/to/cookies.json
```

### Download All Photos from an Album
```bash
./scripts/qzone_photos.py --action download-album --qq 123456789 --album-id "ALBUM_ID" --output /path/to/save/ --cookies /path/to/cookies.json
```

### Upload a Photo
```bash
./scripts/qzone_photos.py --action upload --qq 123456789 --photo "/path/to/image.jpg" --album-id "ALBUM_ID" --cookies /path/to/cookies.json
```

### Create a New Album
```bash
./scripts/qzone_photos.py --action create --qq 123456789 --title "My New Album" --cookies /path/to/cookies.json
```

## Privacy & Security

- This skill accesses QQ Zone using your personal session cookies.
- Cookies and credentials are stored locally and should be kept secure.
- Never share your `cookies.json` file — it grants full access to your QQ Zone account.
- Session cookies expire periodically; re-login will be required.
- This skill uses unofficial QQ Zone APIs and may break if Tencent changes their endpoints.
