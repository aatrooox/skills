# wx - WeChat Draft Workflow Command

Create WeChat Official Account drafts by uploading images and publishing `news` / `newspic` drafts via your backend API.

This command is designed for an internal workflow where z-cli calls the zzao.club API endpoints:

- `POST /api/v1/wx/cgi-bin/token`
- `POST /api/v1/wx/cgi-bin/material/add_material`
- `POST /api/v1/wx/cgi-bin/draft/add`

## Basic Usage

```bash
bunx @zzclub/z-cli wx <subcommand> [options]
```

Available subcommands:

- `token` - Get `access_token`
- `upload` - Upload image materials
- `draft` - Create a `news` draft (HTML content)
- `newspic` - Create a `newspic` draft (text content + image_info)

## Common Options

These options apply to all subcommands:

| Flag | Type | Default | Description |
|------|------|---------|-------------|
| `--base-url <url>` | string | `config.wx.baseUrl` / `https://zzao.club` | API base URL |
| `--pat <token>` | string | `process.env.ZZCLUB_PAT` / `config.wx.pat` | PAT for zzao.club API |
| `--app-id <id>` | string | `process.env.WX_APPID` / `config.wx.appId` | WeChat AppID |
| `--app-secret <secret>` | string | `process.env.WX_APPSECRET` / `config.wx.appSecret` | WeChat AppSecret |
| `--timeout <ms>` | number | `config.wx.timeout` | Request timeout (ms) |

## Subcommands

### wx token

Get `access_token`.

```bash
bunx @zzclub/z-cli wx token --pat <token> --app-id <id> --app-secret <secret>
```

### wx upload

Upload one or more images.

```bash
bunx @zzclub/z-cli wx upload --photos <list>
```

`--photos` supports:

- Comma-separated list: `a.jpg,b.png`
- File paths: `./a.jpg,./b.png`
- HTTP(S) URLs: `https://.../a.jpg`
- Data URLs: `data:image/png;base64,...`
- `file://` URLs

### wx draft

Create a `news` draft with HTML content.

```bash
bunx @zzclub/z-cli wx draft -t "My Title" --html-file ./content.html --photos ./cover.jpg,./p2.png
```

Notes:

- `--html` / `--html-file` content is written directly into WeChat editor `content`.
- If `--photos` is omitted, the command will attempt to extract image URLs from the HTML (markdown and `<img src=...>` are supported).

### wx newspic

Create a `newspic` draft with text content.

```bash
bunx @zzclub/z-cli wx newspic -t "My Title" --content-file ./content.txt --photos ./cover.jpg,./p2.png
```

Notes:

- If `--photos` is omitted, the command will attempt to extract image URLs from the content (markdown and `<img src=...>` are supported).

## Configuration

You can persist defaults via `set`:

```bash
bunx @zzclub/z-cli set --wx-base-url https://zzao.club \
  --wx-pat <token> \
  --wx-app-id <id> \
  --wx-app-secret <secret> \
  --wx-timeout 30000
```

## Agent Workflow

1. Confirm prerequisites (PAT, AppID, AppSecret) exist in config or env.
2. Prefer using `set` to persist values for repeated usage.
3. For draft/newspic:
   - Ensure `-t/--title` is present.
   - Provide `--html-file` / `--content-file`.
   - Provide `--photos` explicitly unless images are already referenced in content.
4. Execute and report the JSON output.

## Troubleshooting

- Missing config values: run `bunx @zzclub/z-cli set --wx-...` or export env vars:
  - `ZZCLUB_PAT`, `WX_APPID`, `WX_APPSECRET`
- Upload failures: confirm the image URL/file exists and is reachable, and increase `--timeout` if needed.
