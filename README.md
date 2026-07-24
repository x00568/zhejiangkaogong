# 管理后台

单文件 HTML 管理端，无需构建、无需服务器，双击 `index.html` 即可运行。

## 打开方式

**方式 A：直接双击**
```
Windows: 右键 → 用 Chrome / Edge 打开
```

**方式 B：本地起个静态服务器（推荐，避免 file:// 协议下某些 API 受限）**
```
cd J:\ClaaudeCode\gongkao-miniprogram\admin
python -m http.server 8080
# 浏览器打开 http://localhost:8080/
```

## 演示账号

| 账号 | 密码 | 角色 | 权限 |
|---|---|---|---|
| `admin` | `admin` | 超级管理员 | 全部模块 |
| `agent1` ~ `agent6` | `agent` | 一级代理 | 仅「我的数据」，只看自己旗下用户 |

## 技术栈

- Vue 3 (UMD, CDN)
- Element Plus (CDN)
- ECharts (CDN)

首次打开需联网加载三个 CDN 资源（约 500KB）；加载后浏览器缓存，离线可继续用。若客户内网无外网，把 `<script>` / `<link>` 的 CDN 换成本地路径即可。

## 数据存储

所有增删改都存在浏览器 **localStorage** 里（key: `gongkao_admin_data_v1`），关闭浏览器不丢；换浏览器/换机器需要重新导入。

想把管理端改的数据同步回小程序 → 点右上角「**导出 data.js**」，浏览器会下载 `jobs.js` 和 `resources.js`，覆盖到 `../data/` 目录即可，小程序重编译就能看到新数据。

## 模块（对照 PRD 2.0 第四节）

| PRD 需求 | 后台模块 | 位置 |
|---|---|---|
| 岗位信息管理 | 岗位管理 | 侧栏第 2 项 |
| 备考资料管理 | 资料管理 | 侧栏第 3 项 |
| 用户与积分管理 | 用户管理 + 积分规则 | 侧栏第 4、5 项 |
| 数据统计模块 | 数据总览 | 侧栏第 1 项 |
| 一级代理后台 | 代理管理 + 我的数据 | 超管从侧栏第 6 项管理；代理登录后自动进入自己的视图 |

## 接入真实后端

管理端的所有操作目前走 localStorage。等后端就绪时，替换点：

1. `loadData()` / `saveData()` 换成 `fetch('/api/admin/xxx')`
2. 增删改的 `saveJob / deleteJob / saveRes / ...` 各方法内改为调 API
3. 登录 `doLogin()` 换成走后端登录接口，拿 token 存 localStorage

代码结构本身已经把「读取 → 修改 → 保存」三步分离，改动量集中在 setup() 顶部约 30 行。

## 注意事项

- CDN 版 UMD 打包大小可以接受，但首屏依赖联网。生产要严格断网可用请下载三个库到本地
- `admin/` 目录会被小程序打包时一并纳入。建议在 `project.config.json` 的 `packOptions.ignore` 里追加 `{"value":"admin","type":"folder"}` 避免多余体积上传
- 演示账号 admin/admin 是硬编码，仅供本地演示，正式部署必须换成后端登录
