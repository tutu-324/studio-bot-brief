# Grok Bot 任务书：兑现「自研软件接 API 做完整一部剧」

你是执行开发的 Grok Bot。本文件是唯一任务源。先读完再写代码。目标不是再做一个大而全的 AI 平台，而是在国庆面试前交出一个**看起来平时就在用的制片工作台**，现场能从剧本跑到竖屏成片。

---

## 0. 一句话任务

基于开源短剧产线二次开发，做出一款桌面/Web 制片系统：

**项目 → 资产 → 分镜 → 排队调用即梦等 API → 人工过镜 → 拼接导出 → 成本账**

同一套引擎覆盖：AI 漫剧、AI 真人剧、抖音带货、宣传片、抖音小视频。

用户已经向面试官说：「我自己做的软件，调的就是即梦家的 API，用来完成一部剧的全制作。」你必须把这句话做成可演示的真东西。

---

## 1. 成功标准（面试当场）

必须同时成立：

1. 打开软件，里面已有历史项目（漫剧一集 + 带货一条 + 宣传片一条），不是空壳。
2. 选中一镜点「重跑」，任务进入队列，能看到状态变化（queued / running / success / failed）。
3. 放出一条 60–90 秒 9:16 成片，角色脸基本锁得住。
4. 切换片型模板（漫剧 ↔ 带货），不必换软件。
5. 成本页能看到：生成次数、通过率、本集花费。
6. 成片在软件内导出，不依赖用户再去即梦网页手工补镜。

做不到第 2、3、5 条，任务失败。

---

## 2. 背景（开发时要对齐）

- 面试公司：新疆凤雏运维科技服务（乌鲁木齐米东）。招聘页是直播带货 / 短视频 / 主播孵化；沟通中明确问了即梦、自研调 API。业务肯定不只有漫剧。
- 时间窗：约 10 天，国庆期间面试。
- 用户会自己准备即梦 / 火山方舟 Key。你负责软件和流水线。
- 演示叙事：这是「制片系统」不是「生成器」。生成器人人会点网页；系统是一部剧在里面做完。

---

## 3. 技术底座（必须从这里二次开发，不要从零造）

按优先级 clone：

### P0 主工程（界面 + 项目/分镜/合成）

- https://github.com/xuanyustudio/LocalMiniDrama  
  MIT · Vue3 + Electron · 本地 SQLite  
  已有：故事→角色/场景/道具→分镜→静帧/视频→整集合成  
  已接：Seedance 2、即梦、火山、可灵、通义  
  同时覆盖 AI 漫剧 / AI 真人剧  
  **这是主 fork 对象。产品名、目录、默认项目全部换掉。**

### P0 生成门禁（API 队列与审核）

- https://github.com/drasstry/shortdrama-pipeline  
  FastAPI + CLI · 火山 Seed 2.0 / Seedream / Seedance  
  原则必须搬过来：**剧本不过审不出人，人不过审不出视频。**  
  生成层、任务状态机优先参考这里，不要在前端里直接散落 fetch。

### P1 片型与分镜契约

- https://github.com/anyhui/video-storyboard-gen-skill  
  广告 / 带货 / 宣传的分镜 JSON 契约，抄字段不要抄空话。
- https://github.com/zenstory-ai/drama-skills  
  短剧/漫剧 skill：原著→剧本→资产→分镜→视频提示词→剪辑→审查。
- https://github.com/A-cat-with-carrots/OnlyShot-ai-short-drama-skill  
  即梦/Seedance 五步流水线、失败模式、分镜 prompt SOP。

### P1 可选参考（不要当主仓）

- https://github.com/waooAI/waoowaoo  大制片场，Next+Docker，体量大，只看信息架构。
- https://github.com/UllrAI/CineGen-ShortDrama  Script→Asset→Keyframe。
- https://github.com/bo961386926/manga-studio  工作流接近，仓太新。
- https://github.com/harry0703/MoneyPrinterTurbo  口播/字幕/配乐拼接，带货和宣传片用，不是锁脸拍剧。
- https://github.com/q1uki/MoneyPrinterAICreate  Turbo + 通义万相分镜视频。

### 不要当产品底盘

- https://github.com/calesthio/OpenMontage  星很多，但是给编码 Agent 的 skill 堆，AGPL 会传染，不适合改成「我的软件」。
- https://github.com/Agions/novella  多智能体导演台，十天做不完，演示虚。

---

## 4. 用户本地参考包（功能对标，禁止当源码搬）

用户提供了这些文件（路径以用户本机/附件为准）：

| 文件 | 是什么 | 你怎么用 |
|---|---|---|
| `huidou-ai-comics.exe` | 灰豆 AI 漫画/漫剧桌面端（约 50MB，Tauri 2 + React） | 只对照功能：画布、分镜、视频接口、多模型中转 |
| `灰豆AI光影引擎_6.7.2_x64-setup.exe` | 灰豆安装包 | 同上，看安装后的模块划分 |
| `uninstall.exe` | 灰豆卸载器 | 忽略 |
| `ffmpeg.exe` | 拼片/抽帧/转码 | 成片导出链路使用，不要重复造编码器 |
| `eva.zip` | 用户对「画布类软件 V3.5 / 灰豆」的功能分析包 | 只读报告和 schema 类型清单，理解市场软件有哪些节点 |
| `agent的技能包或工作流.zip` | 网文写作 / Hermes / 调研等 skill 大礼包 | 剧本拆解、人设、分集可参考网文写作包的结构；SolidWorks/亚马逊包与本任务无关 |

`eva.zip` 里已经整理过的结论（供你对齐产品能力，不要复刻对方前端）：

**画布类软件 V3.5（Tauri + React）常见能力：**
- 模型：可灵 Kling-3.0 / Omni、MiniMax 海螺、万相、即梦 Seedance、豆包 Ark、OpenAI、DeepSeek
- 节点：custom-agent、preview、storyboard-node、storyboard-chart-node、video-input、video-analyze、motion-control、inpaint、jimeng-super-resolution、gen-music、gen-speech、table-editor
- 模块：AssetStore、分镜、本地缓存、OSS 素材

**灰豆 huidou-ai-comics：**
- Konva 2D + three 3D + ReactFlow 节点图
- Vidu / 可灵 / DeepSeek / OpenAI / fal / 本地 :3000 / huidou.tv:3000 `/v1/videos`
- 分镜、TTS、音乐、图床代理

**硬性禁令：**
- 禁止把 eva.zip 里的 bundle 重建文本、JS 碎片、schema 整段粘进新产品。
- 禁止绕过灰豆/画布软件的授权、注册、DRM。
- 禁止把对方商标、文案、资源文件打进发行包。
- 对方软件只提供「市场上已经验证过的功能清单」。实现必须写在开源底盘上。

用户自有产品线索（分析包索引里提到，如本机存在可对接，不存在就不要假设）：
- 产品代码可能在 `D:\导演平台\tutu-canvas`，产品名 eva。
- 若用户本机已有 tutu-canvas，优先把「片型模板 + 即梦队列 + 成本账」接到已有导演台，而不是再开第三套壳。
- 若没有这份代码，就只 fork LocalMiniDrama。

---

## 5. 产品规格（按这个做，不要加戏）

### 5.1 三个核心对象

```text
Project   一部剧 / 一条带货 / 一支宣传片
Asset     人物 | 商品 | 场景 | 音色 | 字幕样式 | 品牌色
Shot      最小生产单位：要生成、要审核、要进时间线
Job       API 任务：queued → running → success | failed → retry
```

### 5.2 五种片型，同一引擎

| type | 输入 | 必绑资产 | 默认镜头结构 |
|---|---|---|---|
| manhua | 剧本 | 角色定妆 + 场景 | 锁脸静帧 → Seedance 出段 |
| live_ai | 剧本 | 仿真人定妆 + 音色 | 图/视频 + 口型（有即梦 OmniHuman 就接，没有就先出无口型段） |
| promo | 卖点文案 + 品牌 | LOGO / 色板 / 场地 | 钩子-说明-收束 |
| shop | 3 条卖点 + 商品图 | SKU 图 + 主播形象 | 钩子 / 展示 / 下单 三镜 |
| douyin | 一句话选题 | 可空 | 1–3 镜快剪 |

### 5.3 流水线（一条不能断）

```text
选题或剧本
  → 拆镜 JSON
  → 绑定资产
  → 人工过资产
  → 队列出图 / 出视频 / TTS
  → 过镜（过 / 废 / 重跑）
  → ffmpeg 时间线拼接 + 字幕
  → 导出 9:16 mp4
  → 写入本集成本
```

### 5.4 镜头 JSON 最小字段（落地就用这套）

```json
{
  "shot_id": "EP01-SC01-SH03",
  "type": "manhua",
  "order": 3,
  "duration_sec": 4,
  "shot_size": "中景",
  "camera": "static",
  "characters": ["role_id_1"],
  "products": [],
  "scene_id": "warehouse_night",
  "action": "女主打开箱盖",
  "dialogue": "这不是我发的货。",
  "image_prompt": "",
  "video_prompt": "",
  "ref_images": ["asset://role_id_1/front"],
  "status": "draft",
  "job_id": null,
  "output_path": null
}
```

status 只允许：`draft | ready | queued | running | needs_review | approved | rejected | exported`

### 5.5 必须自己加的三刀（这是「二次开发」的可见差异）

1. **片型模板切换**（LocalMiniDrama 原仓偏短剧/漫剧，带货和宣传要你补）。
2. **审核门禁**（搬 shortdrama-pipeline：资产未批准禁止出视频）。
3. **成本账**：按 Job 记 provider、model、次数、成功失败、估算金额、本项目合计。

没有这三刀，就是换皮，面试会被看穿。

---

## 6. 样片（软件里要预置，日期不要全是今天）

1. 漫剧 60–90 秒：新疆米东仓库夜戏，特产箱发错，箱底一封旧信。竖屏 9:16。
2. 带货 20–30 秒：同一特产，钩子-展示-下单。
3. 宣传 15 秒：团队/供应链/直播间气氛，不要写成公司简介 PPT。

每个项目里都要有：通过的镜、作废的镜、至少一次重跑记录。

---

## 7. 实施顺序（严格按天，禁止并行开三个大仓）

**D1**  
clone LocalMiniDrama + shortdrama-pipeline。跑通原仓。定产品名（不要用灰豆/即梦/eva 原名）。建数据模型：Project / Asset / Shot / Job。

**D2**  
把片型枚举和镜头 JSON 接到现有分镜表。带货三镜模板先写死也能跑。

**D3–D5**  
接即梦/火山：出图、出视频、失败重试、任务状态。Key 放本地配置，不要写进仓库。  
接入用户提供的 `ffmpeg.exe` 做拼接和抽尾帧当下一镜首帧。

**D6**  
审核门 + 分镜看板（过/废/重跑）。成本账写入。

**D7**  
灌三个样片项目和使用痕迹。

**D8–D9**  
真出一条漫剧成片。锁脸不行就减镜、加参考图，不要加新功能。

**D10**  
写一页「演示口播稿」放仓库 `DEMO.md`：4 分钟点哪里。打包可运行版本。

中途功能蔓延就砍：不要做自动发抖音、不要做完整 NLE、不要做 LoRA 训练、不要做多 Agent 导演。

---

## 8. 技术约束

- 优先 Electron/Vue 桌面端（和 LocalMiniDrama 一致），Windows 可演示。
- 状态机和 API 调用放后端或主进程，渲染进程只展示。
- 所有生成必须进 Job 表，禁止页面里静默调 API。
- 9:16 优先。成片用 ffmpeg concat + 软字幕即可。
- 代码和提交信息用中文或英文均可，但界面必须是中文。
- README 写「自用制片工作台」，不要写「fork 自 xxx 换皮」。被问到开源时的标准答法见下。

面试被问「是不是开源换的」时，用户会说：

> 工作流参考了开源短剧工具，生成队列、片型模板和成本账是自己接的，模型走即梦/火山官方 API。

你的提交要让这句话属实：队列、模板、成本账必须是你写的文件，不要只改标题颜色。

---

## 9. 不要做

- 不要复刻灰豆或画布 V3.5 的 UI/源码。
- 不要引入 AGPL 主依赖（OpenMontage）。
- 不要同时维护 waoowaoo + LocalMiniDrama + 自研三套。
- 不要把网文/亚马逊/SolidWorks skill 整包塞进产品。网文包最多用于「剧本拆分提示词」。
- 不要在仓库里提交 API Key、即梦 Cookie、灰豆安装包、ffmpeg 以外的第三方 exe。
- 不要把「会聊天的 Agent」当成主界面。主界面是项目、资产、分镜、队列、时间线。

---

## 10. 交付清单

- 可运行的 Windows 工作台（或 `npm/pnpm start` 一键起）
- 三个预置项目 + 至少一条可播成片
- `DEMO.md` 四分钟演示脚本
- `COST.md` 说明如何配置即梦/火山 Key、单价怎么估
- 数据目录里能看到 Job 历史

完成本任务的标志：换一台装了 Key 的 Windows 电脑，按 DEMO.md 点一遍，能重跑一镜并导出 mp4。
