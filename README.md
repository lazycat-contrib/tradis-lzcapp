# tradis-lzcapp

[Tradis](https://github.com/coracoo/tradis_docker) 的 LazyCat LPK 打包 —— 面向 NAS 新手的 Docker 管理面板（Go 单容器）。

## 形态

- 单容器镜像：`coracoo/tradis`，端口 8080
- 镜像交付：**mirror 模式**（`docker.1ms.run/coracoo/tradis:{tag}`），digest 校验开启
- 版本发现：三段式 `vX.Y.Z` 发布线，排除 `latest` / `dev` / `agent` 与四段式补丁 tag
- 发布目标：**仅喵喵商店**
- 仅 PC 平台

## docker.sock

Tradis 的职责就是管理宿主 Docker，因此通过 `lzc-build.yml` 的 `compose_override`
保留 `/var/run/docker.sock` 挂载（只读挂载仍等于 daemon 管理能力）。

部署后请在应用内验证 daemon API 是否连通；路径与权限以目标主机实际为准，
构建通过不代表运行时挂载成功。

## 部署参数

| 参数 | 说明 |
|------|------|
| `admin_password` | admin 账号初始密码 |
| `project_root` | Compose 项目的容器内存储路径（挂到 `/lzcapp/var/projects`） |

`JWT_SECRET` 由 `stable_secret` 自动生成并保持稳定，无需手工填写。
数据（数据库与备份）持久化在 `/lzcapp/var/data`。

## 结构

- `package.yml` — LPK v2 包元数据 + 平台限制
- `lzc-manifest.yml` — 单服务、环境变量、文件选择器注入
- `lzc-deploy-params.yml` — 管理员密码与项目路径
- `lzc-build.yml` — 构建配置 + docker.sock 的 compose_override
- `.github/lazycat-action.yml` — 镜像版本源，mirror 交付，仅私有商店
- `.github/workflows/lazycat.yml` — 每日自动跟版 + 发布喵喵商店
