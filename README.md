# 📸 QQ空间相册管理器 (qq-zone-photo)

> 一个用于管理 QQ空间相册的 OpenClaw Skill，支持扫码登录、列出相册、浏览照片、上传照片、下载照片/相册、创建相册。

---

## 🤖 在 AI Agent 中使用

本 Skill 设计为与 AI Agent（如 OpenClaw）配合使用。**AI Agent 在执行任何操作前，需确保虚拟环境已激活且 `cookies.json` 文件有效。**

所有命令以如下格式执行：

```bash
source <项目路径>/.venv/bin/activate && python3 <项目路径>/scripts/qzone_photos.py --action <ACTION> [参数]
```

### Action 速查表

| Action | 说明 | 必填参数 | 可选参数 |
|--------|------|----------|----------|
| `login` | 扫码登录，自动保存 Cookie | `--cookies` | — |
| `list` | 列出所有相册 | `--cookies` | `--qq` |
| `photos` | 浏览相册中的照片 | `--album-id` `--cookies` | `--qq` |
| `upload` | 上传照片到相册 | `--photo` `--cookies` | `--album-id` `--qq` |
| `download` | 下载单张照片 | `--url` `--cookies` | `--output` |
| `download-album` | 下载整个相册 | `--album-id` `--cookies` | `--output` `--qq` |
| `create` | 创建新相册 | `--title` `--cookies` | `--desc` `--qq` |

### 调用示例

```bash
# 扫码登录
python3 scripts/qzone_photos.py --action login --cookies cookies.json

# 列出所有相册
python3 scripts/qzone_photos.py --action list --cookies cookies.json

# 浏览相册照片
python3 scripts/qzone_photos.py --action photos --album-id "ALBUM_ID" --cookies cookies.json

# 上传照片
python3 scripts/qzone_photos.py --action upload --photo "/path/to/image.jpg" --album-id "ALBUM_ID" --cookies cookies.json

# 下载单张照片
python3 scripts/qzone_photos.py --action download --url "PHOTO_URL" --output ./downloads --cookies cookies.json

# 下载整个相册
python3 scripts/qzone_photos.py --action download-album --album-id "ALBUM_ID" --output ./downloads --cookies cookies.json

# 创建新相册
python3 scripts/qzone_photos.py --action create --title "我的新相册" --desc "相册描述" --cookies cookies.json
```

### Agent 工作流示例

#### 场景一：备份指定相册

```
用户: "帮我备份 QQ空间的旅行相册"

Agent 执行:
  1. --action list              → 获取所有相册列表
  2. 从结果中匹配"旅行"相册     → 取得 album-id
  3. --action download-album    → 下载该相册全部照片到本地
  4. 向用户报告下载完成及文件路径
```

#### 场景二：上传照片到指定相册

```
用户: "把桌面上的 vacation.jpg 上传到我的旅行相册"

Agent 执行:
  1. --action list              → 列出所有相册
  2. 从结果中匹配"旅行"相册     → 取得 album-id
  3. --action upload --photo ~/Desktop/vacation.jpg --album-id ALBUM_ID
                                → 上传照片
  4. 向用户确认上传成功
```

#### 场景三：全量备份所有相册

```
用户: "帮我把 QQ空间所有相册都下载下来"

Agent 执行:
  1. --action list              → 获取所有相册列表
  2. 遍历每个相册:
     --action download-album --album-id ALBUM_ID --output ./backup/相册名
                                → 逐个下载每个相册
  3. 汇总报告：共 N 个相册、M 张照片已保存
```

#### 场景四：创建相册并上传多张照片

```
用户: "创建一个叫'2024毕业季'的相册，把这几张照片传上去"

Agent 执行:
  1. --action create --title "2024毕业季"
                                → 创建新相册，获取返回的 album-id
  2. 对每张照片执行:
     --action upload --photo FILE --album-id ALBUM_ID
                                → 逐一上传
  3. --action photos --album-id ALBUM_ID
                                → 验证上传结果
  4. 向用户报告相册创建及上传完成
```

#### 场景五：Cookie 过期后重新登录

```
用户: "帮我看看 QQ空间有哪些相册"（但 Cookie 已过期）

Agent 执行:
  1. --action list              → 尝试列出相册，发现返回认证失败
  2. --action login             → 生成二维码，提示用户扫码
  3. 用户扫码成功              → Cookie 自动保存
  4. --action list              → 重新列出相册
  5. 向用户展示相册列表
```

---

## ✨ 功能特性

| 功能 | 说明 |
|------|------|
| 🔐 扫码登录 | 通过手机 QQ 扫码完成认证，自动保存 Cookie |
| 📋 列出相册 | 查看指定 QQ 号的所有相册信息 |
| 🖼️ 浏览照片 | 查看指定相册中的所有照片详情 |
| ⬆️ 上传照片 | 将本地图片上传到指定相册 |
| ⬇️ 下载照片 | 下载单张照片或整个相册 |
| ➕ 创建相册 | 新建 QQ空间相册 |

---

## 📦 安装

### 1. 创建虚拟环境并安装依赖

```bash
cd qq-zone-photo
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### 2. 扫码登录获取 Cookie

```bash
python3 scripts/qzone_photos.py --action login --cookies cookies.json
```

运行后会弹出二维码图片（macOS 自动在 Preview 中打开），使用手机 QQ 扫码确认登录。成功后 Cookie 自动保存到 `cookies.json`。

> **提示**：Cookie 有效期有限，过期后需重新扫码登录。

---

## 🖥️ CLI 手动使用

### 参数说明

| 参数 | 说明 | 适用 Action |
|------|------|-------------|
| `--action` | 执行的操作（必填） | 所有 |
| `--qq` | QQ 号码（可选，自动从 Cookie 提取） | list / photos / upload / create / download-album |
| `--cookies` | Cookie 文件路径（默认 `cookies.json`） | 所有 |
| `--album-id` | 相册 ID | photos / download-album / upload |
| `--photo` | 本地图片路径 | upload |
| `--url` | 照片 URL | download |
| `--output` | 下载保存目录（默认 `./downloads`） | download / download-album |
| `--title` | 相册标题 | create |
| `--desc` | 相册描述 | create |

### 快速开始

```bash
# 1. 安装依赖
source .venv/bin/activate && pip install -r requirements.txt

# 2. 扫码登录
python3 scripts/qzone_photos.py --action login --cookies cookies.json

# 3. 查看相册列表
python3 scripts/qzone_photos.py --action list --cookies cookies.json

# 4. 上传一张照片
python3 scripts/qzone_photos.py --action upload --photo ./my_photo.jpg --cookies cookies.json

# 5. 下载整个相册
python3 scripts/qzone_photos.py --action download-album --album-id "ALBUM_ID" --output ./backup --cookies cookies.json
```

---

## 🔑 Cookie 格式

`cookies.json` 文件格式：

```json
{
  "qq_number": "123456789",
  "p_skey": "your_p_skey_value",
  "skey": "your_skey_value",
  "uin": "o0123456789"
}
```

获取方式：
- **推荐**：使用 `--action login` 扫码登录，Cookie 自动生成
- **手动**：登录 [QQ空间](https://user.qzone.qq.com/) 后，通过浏览器开发者工具（F12 → Application → Cookies）提取

---

## 📁 项目结构

```
qq-zone-photo/
├── SKILL.md              # Skill 元描述文件
├── _meta.json            # 版本元数据
├── requirements.txt      # Python 依赖
├── README.md             # 本文件
└── scripts/
    └── qzone_photos.py   # 核心脚本
```

---

## ⚠️ 安全与隐私

- Cookie 文件包含 QQ空间完整访问权限，**请勿泄露或分享**
- 凭证仅存储在本地，不会上传到任何服务器
- 会话 Cookie 有时效性，过期后需重新登录
- 本工具使用 QQ空间非官方 API，腾讯更新接口后可能需要适配

---

## 📋 依赖

- Python 3.8+
- requests >= 2.31.0
- Pillow >= 10.0.0
- qrcode >= 7.4.2
- pycryptodome >= 3.19.0
