# 远程 JSON 链接页框架

目标：所有页面数据集中在一个 JSON（`links.json`），分为公共分组 + 多个 profile 分组，模板通用，可让 AI 只改样式/布局而不破坏数据协议。

## 页面与配置
- 入口：`<模板目录>/index.html`
- 集中配置：`<模板目录>/config.js`
  - `profileId`：默认加载的分组 ID
  - `dataBase`：数据根路径（指向存放 `links.json` 的目录，如 `https://raw.githubusercontent.com/<owner>/<repo>/main/data/pages/`）
  - `dataFile`：数据文件名，默认 `links.json`
  - `dataUrl`：可选，直接指定完整 JSON 地址（优先级最高）
- URL 覆盖（可选）：`?id=<id>` 切分组；`&dataUrl=<url>` 或 `&base=<url>` 临时改数据源

## links.json 结构
```json
{
  "common": {
    "buttons": [ { "label": "...", "url": "..." } ],
    "social": [ { "icon": "instagram", "url": "..." } ],
    "footer": {
      "lines": ["...", "..."],
      "cta": { "label": "...", "url": "..." }
    },
    "theme": { "bg": "#...", "text": "#..." }   // 可选，全局主题（对应 CSS 变量 --key）
  },
  "profiles": {
    "<id>": {
      "name": "...",
      "tagline": "...",
      "avatar": "assets/profile.png",
      "social": [ ... ],      // 专属社交，可为空
      "buttons": [ ... ],     // 专属按钮，可为空
      "footer": { ... },      // 可选，覆盖 common.footer
      "theme": { ... }        // 可选，覆盖 common.theme
    }
  }
}
```
字段规则：
- `social.icon` 支持：`instagram` | `tiktok` | `twitter` | `x` | `telegram`（其他值显示首字母）。
- `buttons[*].locked/disabled`：置灰禁用；`type="outline"`：描边按钮。
- 渲染顺序：先专属按钮，若有公共按钮则插入一条玻璃质感分隔线，再渲染公共按钮；无组标题。

## 新增/维护分组
- 在 `profiles` 下复制一个节点，改键名为新的 `<id>`，填入 `name/tagline/avatar/social/buttons` 即可。
- 想让新分组成为默认页：在 `config.js` 把 `profileId` 改成该 `<id>`；或访问时用 `?id=<id>`.

## 模板/样式自定义（给 AI 的约束）
- **不要改动 `app.js` 的数据协议**：继续从 `links.json` 读 `common + profiles.<id>` 并合并。
- 仅在 `styles.css`、`index.html` 做视觉/布局调整；`app.js` 如需改动，只能新增不破坏现有字段含义。
- 如果新模板放在其他目录，复制 `index.html`、`app.js`、`config.js`，更新 `dataBase`/`dataFile` 路径即可。
- 需要分隔线样式，可复用 `.cta-divider`，长度约 140px、居中半透明渐变。

## 本地调试
- 在仓库根目录运行 `python -m http.server 8000`
- 访问 `http://localhost:8000/<模板目录>/`（默认 `profileId`），或 `?id=<id>` 切分组

## 部署建议
- 数据仓库公开放 GitHub Raw / Pages，即可跨域读取；私仓需代理/Worker。
- 如需 SEO，请考虑对每个分组做预渲染静态导出；当前模板为纯前端渲染。

## 演示json
-     "ROSEWU": {
      "name": "♡ ROSEWU 真实原创 ♡",
      "tagline": "♡ 尝试去拍摄一些NTR的相关 真实原创 ♡",
      "avatar": "assets/profile.png",
      "social": [
        { "icon": "instagram", "url": "https://instagram.com" },
        { "icon": "tiktok", "url": "https://tiktok.com" },
        { "icon": "telegram", "url": "https://t.me/BieLaoZaiBot?start=JbzcTHYMumakC" },
        { "icon": "x", "url": "https://x.com/NTRWUROSE" }
      ],
      "buttons": [
        { "label": "TG纸飞机订阅 ✨ 支持人民币", "url": "https://t.me/BieLaoZaiBot?start=JbzcTHYMumakC" },
        { "label": "X (TWITTER)", "url": "https://x.com/NTRWUROSE" },
        { "label": "FANSONE✨ 支持人民币", "locked": true }
      ]
    },
