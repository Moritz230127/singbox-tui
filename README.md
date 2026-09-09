# singbox-tui

A terminal control console for the [sing-box](https://sing-box.sagernet.org/) proxy, built with [Textual](https://textual.textualize.io/). Vim-mode interaction, Catppuccin-style UI, full control and inspection of your self-hosted proxy.

## Features

- **Vim 3-state interaction**: normal (↑↓ module focus) / edit (`:mode` `:node`, ↑↓ options, Enter confirm, Esc save-exit) / command (`:`)
- **Single-screen 4-panel layout**: status indicator | control | traffic | node table (DataTable) + real-time log
- **Node management**: DataTable with node name / server IP / ping (VPS direct) / link latency (via proxy) / download speed; `:node` to select, Enter to switch
- **Full control** (companion script integration): `:start` `:stop` `:mode` `:node` `:speed` `:iface` `:subscribe` `:backup` `:rules-update` `:udp` `:env` `:guard`
- **Monitoring**: real-time traffic rate + session cumulative totals, egress IP, error/connection counts, uptime, block-reject counter
- **Log**: real-time scroll, ERROR red / WARNING yellow, block-reject lines filtered (counted, not displayed), `:log error|warn|all` filter
- **Catppuccin-style UI**: rounded borders, border titles, modern color theme, full fr responsive (all panels scale with terminal width)
- **Performance**: local Clash API via `urllib + asyncio.to_thread`, hierarchical polling (light 3s / heavy 15s), DataTable incremental rendering, persistent `/traffic` stream
- **Terminal too small guard**: width < 100 or height < 20 shows a hint instead of a broken layout

## Requirements

- Python 3.14+
- [textual](https://pypi.org/project/textual/) (Arch: `sudo pacman -S python-textual`)
- sing-box running with clash API on `127.0.0.1:9090` (mixed proxy on `127.0.0.1:7897`)
- Optional companion scripts: `singbox-switch-iface`, `singbox-subscribe`, `singbox-vps-backup`, `singbox-rules-update`, `singbox-udp-health`, `singbox-env-detect`, `singbox` (guard)
- `jq` for the `singbox` CLI

## Install

```bash
sudo pacman -S python-textual jq
cp singbox-tui ~/.local/bin/
cp singbox ~/.local/bin/
chmod +x ~/.local/bin/singbox-tui ~/.local/bin/singbox
```

Optional fish aliases:

```fish
source fish-aliases.fish
```

Or append the contents of `fish-aliases.fish` to `~/.config/fish/config.fish`.

## Usage

### TUI

```bash
singbox-tui
```

### Quick CLI

```bash
singbox status
singbox switch vless-v6
singbox mode auto
singbox nodes
singbox speed
singbox log
singbox tui
```

With fish aliases:

```fish
sb-tui          # 启动 TUI
sb-status       # 服务/节点/出口状态
sb-on           # 开启代理
sb-off          # 关闭代理
sb-restart      # 重启代理
sb-switch <节点> # 切换节点
sb-mode         # 查看/切换模式
sb-nodes        # 节点列表 + 延迟
sb-speed        # 快速测速
sb-udp          # UDP 链路检测
sb-log          # 最近日志
sb-rules        # 更新规则集
sb-backup       # VPS 备份
sb-sub          # 订阅同步
sb-env          # 环境检测
sb-guard        # 健康守卫
sb-sec          # 安全检查
```

### TUI Commands

| Command | Function |
| --- | --- |
| `:start` / `:stop` | Start / stop the sing-box service |
| `:mode` | Edit mode: auto / manual |
| `:node` | Edit mode: select node (DataTable cursor) |
| `:speed` | Speedtest all nodes (ping + link latency + download) |
| `:refresh` | Refresh status |
| `:iface <name>` | Switch network interface |
| `:subscribe [--apply]` | Sync node subscription |
| `:backup` | VPS config backup |
| `:rules-update` | Update rule sets |
| `:udp` | UDP link health check |
| `:env` | Environment detection |
| `:guard` | Health guard |
| `:log error\|warn\|all` | Log filter |
| `:wq` | Save config and exit |
| `:q` | Exit without saving |

### Keys

- `↑`/`↓`: move focus (normal) / move option (edit)
- `Enter`: confirm
- `Esc`: save-exit edit mode (stays in TUI)
- `:`: command mode
- `q`: quit
- Mouse wheel: scroll log only

## Configuration

`~/.config/singbox-tui/config.json` (created on first `:wq`):

```json
{
  "mode": "auto",
  "node": "vless-v6",
  "vps_ip": "YOUR_VPS_IP",
  "speed_urls": [
    ["Cloudflare 10MB", "https://speed.cloudflare.com/__down?bytes=10000000"],
    ["Cloudflare 100MB", "https://speed.cloudflare.com/__down?bytes=100000000"],
    ["ArchLinux 镜像", "https://mirrors.kernel.org/archlinux/iso/latest/archlinux-x86_64.iso.zst"]
  ]
}
```

- `vps_ip`: your VPS IP (used for ping measurement and connection counting)
- clash API secret: `~/.config/singbox-tui.secret` (Bearer token)

## License

MIT
