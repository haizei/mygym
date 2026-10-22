# Exercio · 阶段 2A Token 资产明细（已确认并写入）

流水线进度：阶段 2 / 6 · figma-tokens-system · Tokens 门禁清单
上一阶段已确认：五模块全部P0/P1、18主画板及状态/弹层、Android390×844、英文界面、Studio Dark主流程、六主题对照、练习页样板。

## 盘点与规范架构

- 冷启动模式A：目标Figma仅空Page 1（0:1），无本地Variables、Collections、Text Styles。本轮只读，未写入。
- 已检查订阅库。在Material 3中检索未命中Exercio变量或Manrope样式；通用组件留到Components门禁评估。
- 源码提交3fc5194214588921ace19f9ff881f8ea3b1bc2a7；全量机器数据见exercio-token-source.json。

| 资产 | 拟建方案 | 状态 |
| --- | --- | --- |
| primitives | 单模式原始值，重复值去重，scopes=[] | 待确认 |
| theme | 六主题模式，默认Studio Dark，语义引用原始值 | 待确认 |
| 模式预案 | 如文件限制六模式，改六个单模式集合并保持相同语义路径 | 一并待确认 |
| Text Styles | typography/主题/角色；每主题15个，共90个 | 待确认 |
| 展示 | 01_Design Tokens原生可编辑看板 | 待确认 |
| 源码映射 | description记录Dart路径，ANDROID codeSyntax如visual.colors.canvas；不造CSS变量 | 待确认 |

## 1. Color 资产模块

列顺序SD/PE/AV/HD/PC/MC对应Studio Dark、Prism Echo、Alien Velocity、Handmade Dream、Practice Cartridge、Memento Chrome。八位值采用#RRGGBBAA；全部从ColorTokens直接提取，已识别、待确认。

| 建议变量名称 | SD | PE | AV | HD | PC | MC | 来源区域与用途 | 合并建议 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `color/bg/canvas` | #0B0D10 | #07080B | #080A0E | #0C1A2A | #0D1021 | #09090B | 主题练习截图：画布 | 原始同值去重，语义保留 |
| `color/bg/surface` | #12151A | #0C0E13 | #12161C | #102D2A | #1C2340 | #111114 | 主题练习截图：卡片/底栏 | 原始同值去重，语义保留 |
| `color/bg/elevated` | #1A1E25 | #101827 | #171A20 | #163A36 | #232C50 | #17171B | 主题练习截图：浮层 | 原始同值去重，语义保留 |
| `color/bg/interactive` | #222833 | #20324716 | #1E242D | #1D4741 | #3761802C | #1E1E23 | 主题练习截图：交互背景 | 原始同值去重，语义保留 |
| `color/action/primary` | #E9EDF2 | #EFF2F1 | #00D7FF | #EEE9DB | #9567E8 | #F3F0EA | 主题练习截图：主CTA | 原始同值去重，语义保留 |
| `color/action/on-primary` | #0B0D10 | #07080B | #04070A | #0C1A2A | #0D1021 | #09090B | 主题练习截图：主CTA文字 | 原始同值去重，语义保留 |
| `color/action/secondary` | #7FC8FF | #4776E6 | #1479FF | #8BC6C4 | #37D5E8 | #BCC3CA | 主题练习截图：次级强调 | 原始同值去重，语义保留 |
| `color/action/on-secondary` | #06131C | #EFF2F1 | #DCE3EA | #0C1A2A | #0D1021 | #09090B | 主题练习截图：次级文字 | 原始同值去重，语义保留 |
| `color/accent` | #7FC8FF | #58DDE5 | #00D7FF | #A9D2DE | #F3C94C | #A77A96 | 主题练习截图：高亮/进度 | 原始同值去重，语义保留 |
| `color/text/primary` | #F4F6F8 | #EFF2F1 | #DCE3EA | #EEE9DB | #FFF4D6 | #F3F0EA | 主题练习截图：正文/标题 | 原始同值去重，语义保留 |
| `color/text/secondary` | #A8B0BC | #8E97A3 | #8D99A8 | #A9BDBC | #B9C0DE | #BCC3CA | 主题练习截图：辅助说明 | 原始同值去重，语义保留 |
| `color/text/disabled` | #5C6472 | #4A515C | #4C5663 | #63807D | #6A739B | #5A5F66 | 主题练习截图：禁用文字 | 原始同值去重，语义保留 |
| `color/border/default` | #2A303A | #1B2230 | #2B3542 | #1F4B45 | #3B4680 | #34383D | 主题练习截图：描边 | 原始同值去重，语义保留 |
| `color/border/divider` | #1E232B | #12171F | #1B222B | #163732 | #2A3358 | #1C1C21 | 主题练习截图：分隔线 | 原始同值去重，语义保留 |
| `color/state/focus` | #7FC8FF | #58DDE5 | #00D7FF | #8BC6C4 | #37D5E8 | #A77A96 | 主题练习截图：焦点 | 原始同值去重，语义保留 |
| `color/state/selected` | #7FC8FF | #58DDE5 | #00D7FF | #8BC6C4 | #37D5E8 | #A77A96 | 主题练习截图：选中 | 原始同值去重，语义保留 |
| `color/state/unselected` | #39414D | #232C3B | #2B3542 | #244F49 | #3B4680 | #2A2E33 | 主题练习截图：未选/轨道 | 原始同值去重，语义保留 |
| `color/state/success` | #4ADE80 | #6FD9A6 | #3DDC91 | #829B6E | #A5D94A | #78BBB7 | 主题练习截图：Clean/目标速度 | 原始同值去重，语义保留 |
| `color/state/warning` | #FACC15 | #F5C84C | #FFD43B | #DF926F | #F3C94C | #D3A6B8 | 主题练习截图：Shaky | 原始同值去重，语义保留 |
| `color/state/failure` | #F87171 | #E74B3C | #F2582A | #C78C98 | #EF5A9D | #C94C59 | 主题练习截图：Failed | 原始同值去重，语义保留 |
| `color/state/neutral` | #8A93A0 | #7A8391 | #7E8996 | #887EAD | #8C95BF | #7C828A | 主题练习截图：Unrated | 原始同值去重，语义保留 |

透明值保留：PE源码Color(0xFF16203247)按低32位ARGB为#20324716，alpha22/255；PC源码Color(0xFF2C376180)为#3761802C，alpha44/255。拟保留源码，不擅自改为不透明色；截图合成色取决于底层。
Scopes：背景FRAME_FILL/SHAPE_FILL，文字TEXT_FILL，描边STROKE_COLOR；多用途变量仅添加必要作用域并集。

## 2. Typography 资产模块

Studio Dark基线如下；没有显式Flutter height的角色用Figma AUTO，不伪造精确行高。

| 建议Style命名（前缀typography/studio-dark/） | 字号px | 字重 | 行高 | 来源区域与使用场景 | 状态 |
| --- | --- | --- | --- | --- | --- |
| `display/lg` | 34 | Manrope 600 | AUTO | 段落展示 | 源码已识别，待确认 |
| `display/md` | 26 | Manrope 600 | AUTO | 次级展示 | 源码已识别，待确认 |
| `timer/lg` | 76 | Space Grotesk 600 | AUTO | 主计时 | 字重替代待确认 |
| `timer/compact` | 28 | Space Grotesk 600 | AUTO | 紧凑计时 | 字重替代待确认 |
| `heading/lg` | 24 | Manrope 600 | AUTO | 大标题 | 源码已识别，待确认 |
| `heading/md` | 20 | Manrope 600 | AUTO | AppBar | 源码已识别，待确认 |
| `heading/sm` | 17 | Manrope 600 | AUTO | 卡片标题 | 源码已识别，待确认 |
| `body/lg` | 17 | Manrope 400 | 24.65/145% | 长正文 | 源码已识别，待确认 |
| `body/md` | 15 | Manrope 400 | 21.75/145% | 正文 | 源码已识别，待确认 |
| `body/sm` | 13 | Manrope 400 | 18.85/145% | 辅助说明 | 源码已识别，待确认 |
| `label/lg` | 15 | Manrope 600 | AUTO | 主按钮 | 源码已识别，待确认 |
| `label/md` | 13 | Manrope 500 | AUTO | 标签 | 源码已识别，待确认 |
| `numeric/lg` | 40 | Space Grotesk 600 | AUTO | 练习BPM | 字重替代待确认 |
| `numeric/md` | 22 | Space Grotesk 600 | AUTO | 次级数字 | 字重替代待确认 |
| `metronome/readout` | 52 | Space Grotesk 700 | AUTO | 节拍器BPM | 源码已识别，待确认 |

其他主题共用角色：display/md=display/lg−8；heading24/20/17、600；body17/15/13、400；label15/13、600/500；numeric40/22、600；metronome52、700；timer/compact28、主题timerWeight。数字使用等宽数字意图，numeric/metronome字距沿用timerLetterSpacing。

| 主题 | Display/Heading/Body/Label/Numeric字族 | Display字号/字重/字距 | Timer字号/字重/字距 | Body行高 |
| --- | --- | --- | --- | --- |
| Studio Dark | Manrope / Manrope / Manrope / Manrope / Space Grotesk | 34/600/0 | 76/600/0 | 145% |
| Prism Echo | Cormorant / Space Grotesk / Space Grotesk / Space Grotesk / Space Grotesk | 40/300/3 | 88/300/2 | 145% |
| Alien Velocity | Orbitron / Rajdhani / Space Grotesk / Rajdhani / Space Grotesk | 30/700/2 | 92/700/1 | 145% |
| Handmade Dream | Space Grotesk / Manrope / Manrope / Manrope / Space Grotesk | 34/300/4 | 80/300/1 | 160% |
| Practice Cartridge | Press Start 2P / Press Start 2P / Manrope / Manrope / Press Start 2P | 18/400/1 | 46/400/2 | 145% |
| Memento Chrome | Bodoni Moda / Space Grotesk / Space Grotesk / Space Grotesk / Space Grotesk | 38/400/1 | 84/200/3 | 145% |

字体替代待确认：Manrope SemiBold可用；Space Grotesk仅Bold/Light/Medium/Regular，缺600和200，建议Figma中600→Medium500、200→Light300。Press Start 2P仅Regular可用，源码共享构建器中该字体的600/700角色建议使用Regular400。保留字族字号字距并标注近似，源码原值不变；其他所需字族与字重均已发现。严格一致方案需先补齐Figma可用字重。

## 3. Spacing & Layout 资产模块

| 建议变量名称 | SD | PE | AV | HD | PC | MC | 测量来源/合并建议 | 拟应用场景 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `spacing/xs` | 4 | 6 | 4 | 6 | 4 | 6 | 源码SpacingTokens，不跨主题归一化 | Auto Layout gap/padding |
| `spacing/sm` | 8 | 12 | 8 | 10 | 8 | 12 | 源码SpacingTokens，不跨主题归一化 | Auto Layout gap/padding |
| `spacing/md` | 16 | 20 | 12 | 18 | 16 | 20 | 源码SpacingTokens，不跨主题归一化 | Auto Layout gap/padding |
| `spacing/lg` | 24 | 32 | 18 | 28 | 24 | 30 | 源码SpacingTokens，不跨主题归一化 | Auto Layout gap/padding |
| `spacing/xl` | 32 | 44 | 26 | 40 | 32 | 42 | 源码SpacingTokens，不跨主题归一化 | Auto Layout gap/padding |
| `spacing/xxl` | 48 | 64 | 38 | 56 | 48 | 60 | 源码SpacingTokens，不跨主题归一化 | Auto Layout gap/padding |
| `size/touch-min` | 48 | 48 | 48 | 48 | 48 | 48 | ErgonomicTokens | 最小触控区域 |
| `size/control-primary` | 64 | 64 | 64 | 64 | 64 | 64 | ErgonomicTokens | 主操作高度 |
| `size/navigation-indicator` | 3 | 3 | 3 | 3 | 3 | 3 | theme_builder | 导航标记 |

页面和卡片边距绑定spacing/md等角色，不另造近似值；Studio Dark计时卡padding横24、纵16。390×844为确认视口，系统安全区独立处理，样板阶段结合截图校准。间距GAP，尺寸WIDTH_HEIGHT。

## 4. Geometry & Effects 资产模块

| 资产类型/建议变量名称 | SD | PE | AV | HD | PC | MC | 拟应用场景 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `radius/default` | 10 | 4 | 2 | 20 | 0 | 2 | ShapeTokens对应属性 |
| `radius/card` | 12 | 4 | 2 | 24 | 0 | 3 | ShapeTokens对应属性 |
| `radius/control` | 10 | 3 | 2 | 28 | 0 | 2 | ShapeTokens对应属性 |
| `radius/input` | 10 | 3 | 2 | 20 | 0 | 2 | ShapeTokens对应属性 |
| `stroke/default` | 1 | 1 | 1.5 | 1 | 2 | 0.75 | ShapeTokens对应属性 |
| `stroke/selected` | 2 | 2 | 3 | 2 | 4 | 1.5 | ShapeTokens对应属性 |
| `size/progress-thickness` | 6 | 2 | 14 | 10 | 16 | 2 | 对应进度风格组件 |
| `progress/segments` | 0 | 0 | 0 | 0 | 16 | 0 | 对应进度风格组件 |
| `progress/segment-gap` | 0 | 0 | 0 | 0 | 3 | 0 | 对应进度风格组件 |

圆角CORNER_RADIUS、描边STROKE_FLOAT；segments等离散配置保留组件说明/Variant语义，不强行绑定不支持的属性。

| 效果资产 | 数值/参数 | 拟应用场景 |
| --- | --- | --- |
| Studio Dark | 无阴影 | 不新增无证据投影 |
| Handmade Dream卡片 | offset0,2；Flutter blurRadius18；#00000033 | 专用Effect Style，Figma blur视觉对应需样板校准 |
| Cartridge卡片 | #090B18、blur0、offset0,0 | 源码offset由blur>0判断，不按注释臆造硬偏移 |
| PE/AV计时光晕 | cyan半径28/16 | 局部主题组件，不抽全局通用阴影 |

## 5. 局部保留与单次装饰资产清单

| 资产 | 来源 | 处理 |
| --- | --- | --- |
| Prism光谱进度、背景渐变 | prism_echo.dart | 主题组件内部保留，不扩展通用全局色阶 |
| Alien几何装饰 | alien_velocity.dart | 局部原生矢量，业务不依赖装饰 |
| Handmade圆弧与渐变 | handmade_dream.dart | 主题组件内部保留 |
| Cartridge像素分段和图标 | practice_cartridge.dart | 局部布局/风格 |
| Memento颗粒和装饰 | memento_chrome.dart | 局部原生效果近似需验证，不整页贴图 |
| 六套音色合成参数 | AudioProfile | 交互说明，不作视觉变量，不声称Figma运行Android音频 |
| 长按450ms、重复100ms/45ms | metronome_panel.dart | 组件交互说明，不发明全局Motion缓动/时长Token |

## 确认后执行范围

1. 创建已确认原始值、语义变量、六主题取值、作用域、代码映射、90个Text Styles及所列Effect Style。
2. 在01_Design Tokens创建原生可编辑规范看板，保留字体近似和效果边界。
3. 验证集合/变量/样式数量、取值、alias、作用域、字体加载和展示。
4. Token写入后另行输出Components明细，等待确认再创建主组件。

本轮请确认：阶段2 Tokens全部明细，含字体字重替代、源码透明度保留、六模式上限预案。

门禁依据：figma-prototype-pipeline要求“Tokens与Components仍分两道门禁：先确认Tokens再出Components明细”；figma-tokens-system要求上述五类清单确认后才写入01_Design Tokens。
