[README.md](https://github.com/user-attachments/files/35209072/README.md)
# Golden-hour.
一款本地化的《使命召唤》风格射击游戏。枪械和皮肤均可自定义。非常适合在飞机上放松娱乐。# 暮色酒店（Golden Hour）

一个纯前端的网页第一人称射击游戏（FPS），可在浏览器里直接玩。支持单人训练（打机器人），也预留了联机 PvP 的接口。枪械、瞄具、音效等美术资源全部由代码程序化生成，无需外部模型文件。

## 快速开始

最简单：双击项目根目录的 `start.command`。它会在 `8080` 端口启动本地服务并自动打开浏览器。

手动启动：

```bash
cd /path/to/golden-hour
python3 -m http.server 8080 --bind 127.0.0.1
```

然后浏览器访问 <http://localhost:8080/>。

> 必须通过 HTTP 服务打开，不能直接双击 `index.html` 用 `file://` 打开——游戏用 ES module 和 `fetch` 加载资源，`file://` 协议会被浏览器拦截。
>
> 若 8080 端口被占用，可换端口（例如 `PORT=8081` 时 `start.command` 会读取该变量），或手动指定其他端口后修改访问地址。

## 操作

默认键鼠绑定（可在设置里自定义，最多每动作 4 个键）：

| 动作 | 按键 |
| --- | --- |
| 移动 | `W/A/S/D` 或方向键 |
| 冲刺 | `Shift` |
| 跳跃 / 翻越 | `Space` |
| 蹲下 / 滑铲 | `C` / `Ctrl` |
| 趴下 / 飞扑 | `X` / `Z`（仅训练） |
| 开火 | 鼠标左键 |
| 瞄准 | 鼠标右键 / 中键 |
| 换弹 | `R` |
| 近战 | `V` / `F` / 鼠标侧键 |
| 手雷 | `G` / `Q`（仅训练） |
| 主武器 / 副武器 | `1` / `2` |
| 切换武器 | `E` / 滚轮 |
| 检视武器 | `T` |
| 查看战绩 | 按住 `Tab` |
| 呼叫 K9 | `B` / `3`（仅训练） |

也支持手柄（左摇杆移动、右摇杆转向、LT 瞄准、RT 开火等）、触屏虚拟按键，以及移动端陀螺仪瞄准。

## 玩法与内容

- **枪械**：KR-7 突击步枪、STRIX 9 手枪、VESPER 9 冲锋枪、M12 霰弹枪。每把枪有独立的弹道、后坐力、散布、换弹和音效。
- **枪匠**：可更换瞄具、枪口、下挂、弹匣、枪托，实时改变武器数值与外观；爆头可解锁迷彩。
- **地图与模式**：内置单人训练地图，从 `assets/maps/catalog.json` 读取；联机模式通过服务端目录 `/api/catalog` 提供。
- **进度**：本地存档，可同步 B站 Toy 身份后使用云存档。
- **中文本地化**：`i18n-zh.js` 负责界面文案翻译。

### 联机说明

联机功能需要额外的后端服务（提供 `/api/config`、`/api/catalog`、`/api/maps/*` 和 WebRTC 信令）。未配置后端时，以本地 HTTP 服务打开即可正常进行单人训练；若页面配置的 API 地址为空字符串，联机入口会关闭。

## 当前版本的自定义改动

此副本在原始项目基础上做了两处玩法调整：

1. **武器全部解锁**：取消 B站「关注 + 三连」解锁条件，四把枪开局即可使用。逻辑在 `client/toy-weapons.js` 的 `canUse()`，现恒返回 `true`。
2. **支持跑射**：边冲刺边开火。实现于 `dist/game.js`，移除了冲刺对开火的拦截（`sprintLock` 判定）以及开火对冲刺的打断（`blocksSprint()` 中的 `sinceFire` 判定）。

## 项目结构

```
golden-hour/
├── index.html              页面入口，加载 i18n 与 client/bootstrap.js
├── i18n-zh.js              中文文案
├── start.command           一键启动脚本
├── client/                 运行时加载的源码（ES module）
│   ├── bootstrap.js        启动流程，加载引擎与各模块
│   ├── pvp.js              联机/PvP 入口
│   ├── toy-weapons.js      武器解锁门槛（当前版本已放开）
│   ├── key-bindings.js     键鼠绑定
│   ├── gamepad-controls.js 手柄支持
│   ├── touch-controls.js   触屏控制
│   ├── gyro-aim.js         陀螺仪瞄准
│   ├── catalog.js          地图/模式目录校验
│   ├── maps/               训练地图加载与场景
│   └── modes/              游戏模式（团队等）
├── dist/game.js            游戏引擎打包文件（渲染、武器模型、动画、音效）
├── assets/                 预烘焙资源（武器外观、地图数据、角色数据）
└── README.md
```

> 注意：`client/*.js` 是运行时真正加载的源码；`dist/game.js` 是引擎打包文件，也由 `index.html` 直接加载。修改后刷新浏览器即可生效（必要时强制刷新清缓存）。

## 环境要求

- 现代浏览器（支持 WebGL2、ES module、Web Audio）
- Python 3（仅用于本地静态服务；任何静态文件服务器均可替代）

