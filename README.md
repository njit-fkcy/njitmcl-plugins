# NJIT MCL 插件市场

本仓库只存放 NJIT MCL 启动器的插件发布包与元数据，**不包含启动器本体代码**。

启动器本体更新只覆盖固定文件清单，`PCL\Plugins\` 目录不参与更新，插件与本体物理隔离。

## 提交权限

**只有管理员可以提交插件。** 本仓库属于 `njit-fkcy` 组织，仅组织管理员拥有写权限；普通用户无法发布 Release。
如需上架插件，请联系管理员。

## 发布约定

每个插件对应**一个 Release**，tag 格式：

```
<plugin-id>-v<version>
```

例如 `server-tool-v1.0.0`。

每个 Release 需上传**一个 zip 资产**：

| 资产 | 说明 |
|---|---|
| `<plugin-id>-<version>.zip` | 插件包，解压后根目录包含 `plugin.json` 与入口程序 |

上架信息由仓库根目录的 [`plugins.json`](plugins.json) 统一维护，官网中控只读这个索引，再按 `id + 版本` 匹配对应 Release 的 zip 下载地址。
索引条目中的 `entry` 必须与 zip 内 `plugin.json` 的 `entry` 一致。

### plugin.json

```json
{
  "id": "server-tool",
  "name": "社团服务器工具",
  "version": "1.0.0",
  "entry": "ServerTool.exe",
  "minLauncherVersion": "1.8.0",
  "description": "一键查看社团服务器状态、快捷加入服务器。",
  "author": "NJIT MCL",
  "homepage": "https://njitmc.club"
}
```

字段说明：

| 字段 | 必填 | 说明 |
|---|---|---|
| `id` | 是 | 插件唯一标识，小写字母、数字、短横线，长度 2–40 |
| `name` | 是 | 显示名称，长度 1–40 |
| `version` | 是 | 语义化版本，如 `1.0.0` |
| `entry` | 是 | 入口程序相对路径，必须是 zip 内的可执行文件（`.exe`） |
| `minLauncherVersion` | 是 | 最低启动器版本，低于该版本不展示 |
| `description` | 是 | 一句话简介，长度 1–200 |
| `author` | 否 | 作者署名 |
| `homepage` | 否 | 主页或说明页地址，必须是 https |

### 插件包结构

```
server-tool-1.0.0.zip
├── plugin.json
├── ServerTool.exe
└── ...（插件自带依赖）
```

## 安全约束

1. 插件以**独立进程**运行，启动器不加载任何第三方代码进本体进程。
2. 插件不得修改启动器本体文件、更新清单或 `PCL\Plugins\` 以外的目录。
3. 启动器只校验 `plugin.json` 与入口文件是否存在，不做代码签名校验；管理员上架前需自行确认包内容。
4. 插件安装目录为 `PCL\Plugins\<id>\<version>\`，不同版本可共存。

## 发布步骤（管理员）

1. 本地准备好 `plugin.json` 与插件目录，打成 `<plugin-id>-<version>.zip`（`plugin.json` 位于 zip 根目录）。
2. 在本仓库创建 Release，tag 使用 `<plugin-id>-v<version>`，上传 zip 资产。
3. 在仓库根目录 `plugins.json` 的 `plugins` 数组里追加或更新该插件条目，提交到 `main`。
4. 官网 `/api/plugins` 有 10 分钟缓存，等待缓存过期后启动器即可看到新插件。

> 只发 Release 而不更新 `plugins.json`，插件不会出现在市场里；索引与 Release 的版本号必须一致。
