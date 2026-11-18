# Cloady Fleet · 自动更新通道

本仓库是 Cloady Fleet 的公开更新通道，只存放运行时发布产物与版本清单，不包含源代码。

## 目录结构

```
version.json                       # 最新版本清单
releases/cloady-fleet-<ver>.tar.gz # 可直接解压运行的发布包
```

`version.json` 字段：

| 字段 | 说明 |
| --- | --- |
| `version` | 语义化版本号 |
| `released_at` | 发布时间（ISO 8601） |
| `notes` | 版本说明 |
| `min_version` | 允许直接升级的最低版本 |
| `artifact` | 发布包相对路径 |
| `sha256` | 发布包 SHA-256 |
| `size` | 发布包字节数 |

## 服务器更新器

主机侧更新器读取：

```
https://raw.githubusercontent.com/xbox-cn/cloady-fleet-updates/main/version.json
```

下载发布包后校验 `sha256` 与 `size`，解压到 `app.new`，校验关键文件，原子替换 `app`，重启 `cloady-fleet` 并做健康检查；失败自动回滚到 `app.old`。更新器只替换应用目录，不会触碰 `data/`、`.env` 或数据库。

镜像地址（中国大陆加速）：

```
https://ghfast.top/https://raw.githubusercontent.com/xbox-cn/cloady-fleet-updates/main/version.json
```

## 发布流程

源代码仓库打标签 `v*` 或手动触发 `release` workflow：

1. 构建前端与发布包
2. 计算 SHA-256
3. 通过部署密钥推送 `version.json` 与 `releases/*.tar.gz` 到本仓库

发布包内包含 `VERSION`、`backend/`、`frontend/dist/`、`package.json`、`README.md`、`.env.example` 与运维脚本，不包含 `node_modules`、数据库或任何密钥。

## 校验

```bash
curl -fsSL https://raw.githubusercontent.com/xbox-cn/cloady-fleet-updates/main/version.json
sha256sum cloady-fleet-1.1.0.tar.gz
```
