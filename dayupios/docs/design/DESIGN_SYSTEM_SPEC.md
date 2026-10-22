# openGym Design System — 代码提炼与合并清单

状态：用户已确认，阶段 A 基础资产与本批流程所需的阶段 B Master Components 均已写入 Figma。
阶段 B 结果：95 个 Master Components（含 32 个源 SVG 图标），13 个变体集合；组件与实例使用记录见 FLOW_ASSEMBLY_SPEC.md 和 figma-flow-validation.json。
实施结果：opengym/theme（light/dark）36 个颜色变量；opengym/foundation 39 个数值变量；17 个 Text Styles；4 个 Effect Styles。展示页包含 72 个绑定色块、17 个样式文字样例和 4 个效果样例。
Figma 展示入口：https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=70-2
已核对模式值、别名、scopes、WEB code syntax 和样式参数；比较数值采用 0.0001 容差，scopes 按无序集合比较。已修正色卡行高度并检查渲染，无行内容溢出。
资产 ID 记录见 figma-foundations-state.json；完整值与验证记录见 figma-foundations-validation.json。
SF Pro 不可用，17 个文字样式使用 Inter；说明与中文展示使用 Noto Sans SC。
日期：2026-10-20。目标：lvFX678GbPoE2IZPjYEybP / opengym。
模式：首次建立原生资产；沿用前轮已确认的架构与说明板。
来源：frontend/src/index.css、components/ui.jsx、components/TabBar.jsx、lib/accent.js、lib/format.js。
数值来自源码静态读取；未通过 App 运行截图核对平台字体度量。此文件记录已实施的阶段 A 与后续组件预案，不修改 App 代码。

## 1. 合并与变更决策

| 状态 | 内容 | 处理与影响 |
|---|---|---|
| KEEP | 已有 7 个 Figma Page 和架构说明板 | 保留原节点；原生资产添加到对应分区 |
| KEEP | 深浅主题、默认绿色、圆角 8/12/14/16/22、间距 16 | 沿用已确认方向；以 CSS 最终规则为准 |
| UPDATE | 说明中的浅色绿色按钮前景白色 | 更正为黑色；:root[data-theme="light"][data-accent="lime"] 覆盖基础值，不是修改产品颜色 |
| ADD | 颜色变量、数值变量、Text Styles、组件级 Effect Styles | 首轮绿色情况 Light/Dark；已有说明板尚不是这些原生资产 |
| ADD | 常用控制的 token 映射与组件变体计划 | Tokens 先落地；Master Components 依技能阶段 B 另行确认 |
| CONFLICT | iOS 系统字体与 Figma 可用字体 | 优先检查 SF Pro；若不可用用 Inter + Noto Sans SC 展示，明确为字体替代 |
| DEPRECATE | 无 | 不删除既有内容 |

合并原则：同值不同语义可共用基础值，但保留语义别名，例如 spacing/page → spacing/16、radius/card → radius/14。颜色相同但职责不同也保留别名；brand/primary 与 status/success 不合为同一个语义项。

不建议把 15/16/17px 字号、7/8px 间距、12/14px 圆角强行归一：代码有明确的小按钮、列表、正文、标签和嵌套控件差异。本轮是代码还原，非视觉改版。

## 2. 颜色变量 / color

建议 collection：opengym/theme，modes：light、dark。若团队方案不允许第二 mode，拆成 light/dark 两个 collection，并记录对应关系。
变量使用具体 scopes（背景 FRAME_FILL/SHAPE_FILL、文字 TEXT_FILL、描边 STROKE_COLOR）。半透明色保留 alpha，不预混到某一背景。

| 小写路径 | dark | light | CSS 来源 |
|---|---|---|---|
| color/bg/default | #000000 | #f2f2f7 | --bg |
| color/bg/elevated | #0e0e10 | #f7f7fa | --bg-el |
| color/surface/default | #1c1c1e | #ffffff | --surface |
| color/surface/secondary | #2c2c2e | #ececef | --surface-2 |
| color/surface/tertiary | #3a3a3c | #e3e3e8 | --surface-3 |
| color/text/primary | #ffffff | #000000 | --label |
| color/text/secondary | rgba(235,235,245,.60) | rgba(60,60,67,.60) | --label-2 |
| color/text/tertiary | rgba(235,235,245,.32) | rgba(60,60,67,.30) | --label-3 |
| color/text/quaternary | rgba(235,235,245,.18) | rgba(60,60,67,.16) | --label-4 |
| color/border/default | rgba(84,84,88,.60) | rgba(60,60,67,.29) | --sep |
| color/border/overlay | rgba(84,84,88,.34) | rgba(60,60,67,.20) | --sep-op |
| color/palette/blue | #0a84ff | #007aff | --blue |
| color/palette/green | #30d158 | #34c759 | --green |
| color/palette/red | #ff453a | #ff3b30 | --red |
| color/palette/orange | #ff9f0a | #ff9500 | --orange |
| color/palette/yellow | #ffd60a | #ffcc00 | --yellow |
| color/palette/teal | #40c8e0 | #30b0c7 | --teal |
| color/palette/indigo | #5e5ce6 | #5856d6 | --indigo |
| color/palette/pink | #ff375f | #ff2d55 | --pink |
| color/palette/purple | #bf5af2 | #af52de | --purple |
| color/palette/mint | #63e6e2 | #00c7be | --mint |
| color/palette/brown | #ac8e68 | #a2845e | --brown |
| color/palette/grey | #8e8e93 | #8e8e93 | --grey |
| color/brand/primary | alias palette/green | alias palette/green | --acc，默认 lime |
| color/brand/pressed | #248a3d | #248a3d | --acc-2，默认 lime |
| color/brand/on-primary | #000000 | #000000 | --on-acc 的最终 lime 规则 |
| color/brand/soft | primary alpha .16 | primary alpha .16 | --acc-soft |
| color/brand/outline | primary alpha .38 | primary alpha .38 | --acc-line |
| color/status/success | alias palette/green | alias palette/green | 完成状态 |
| color/status/warning | alias palette/orange | alias palette/orange | 进行中/警示 |
| color/status/danger | alias palette/red | alias palette/red | 错误/删除 |
| color/status/info | alias palette/blue | alias palette/blue | 信息入口 |
| color/overlay/scrim | rgba(0,0,0,.40) | rgba(0,0,0,.40) | .mback |

派生透明色在 Figma 中保存为显式 RGBA，关联与计算公式写入 description；不声称存在可自动做 alpha 乘法的变量表达式。别名用于不带计算的同类型值。

组件语义补充：sheet 背景 dark → bg/elevated，light → bg/default；segmented 轨道 dark → surface/tertiary，light → rgba(118,118,128,.12)；tabbar dark → bg/elevated alpha .94，light → rgba(249,249,251,.94)。这些保留在组件语义命名空间，不替换全局 surface。

八种强调色预设：lime、sky、orange、violet、pink、red、teal、gold。首轮提供默认 lime 双主题；其余预设记录为扩展表而不生成 16 个主题 mode。custom 颜色由 accent.js 根据主题运行时校正，作为行为说明，不虚构静态 token 值。

## 3. 数值变量

建议 collection：opengym/foundation，mode：default；按类型设置 GAP、CORNER_RADIUS、STROKE_FLOAT、OPACITY 等 scopes。

| 命名 | 数值 | 来源与用途 |
|---|---|---|
| spacing/2、4、6、7、8、10、11、12、13、14、15、16、18、20、22、24 | 对应 px | CSS 多处实际使用；11 来自行行内边距，13/15 来自输入框 |
| spacing/page | alias spacing/16 | --pad，#app |
| spacing/card | alias spacing/16 | .card padding |
| spacing/row-gap | alias spacing/12 | .row、.lrow |
| radius/6、7、8、9、10、12、14、16、22 | 对应 px | tag、图标容器、按钮、segmented、搜索/stepper、卡片、确认框、sheet |
| radius/card | alias radius/14 | --r-card |
| radius/control | alias radius/12 | --r |
| radius/sheet | alias radius/22 | --r-xl |
| radius/pill | 99 | .chip、.sw；圆形节点使用等宽高椭圆几何，不用无限圆角替代所有形状 |
| border/hairline | .5 | --hair |
| border/icon | 1.7 | --icon-stroke |
| border/focus | 2.5 | :focus-visible；输入框自身 focus ring 为 2 |
| opacity/disabled-control | .32 | .btn、.iconbtn |
| opacity/disabled-switch | .40 | .sw |
| motion/fast | 140 | --fast，毫秒元数据 |
| motion/medium | 220 | --med，毫秒元数据 |

motion/ease = cubic-bezier(.32,.72,0,1) 记录于规范；不将 CSS 贝塞尔字符串冒充可直接绑定的数字变量。sheet 的 340ms、center 的 280ms 保留组件参数。

组件尺寸留在组件参数：icon-button 36×36；row min-height 46、padding 11×14；switch 51×31、knob 27；stepper 按钮 40×44；check 30×30；slider knob 26、track 6。触摸扩展命中区域保留交互注释，不把可见尺寸统一改为 44。

安全区、键盘高度、同步条高度是运行时环境值；不当作固定 0/44/34px 全局变量。390×844 是原型展示规格。

## 4. Text Styles

正文系统栈：-apple-system、BlinkMacSystemFont、SF Pro Text、SF Pro Display、Segoe UI、Roboto、system-ui。先确认 Figma 可用字体；不把一个系统栈视为可直接导入的字体。
下面 letter-spacing 用百分比还原 em；line-height 用百分比。颜色通过 text fill variables 绑定，字体样式不锁死主题颜色。

| Style | px / weight | line-height | letter-spacing | 代码来源 |
|---|---|---|---|---|
| typography/title/large | 34 / 700 | 106% | -2.8% | .hdr h1，宽度大于 420 |
| typography/title/mobile | 30 / 700 | 106% | -2.8% | .hdr h1，宽度小于等于 420 |
| typography/metric/large | 30 / 600 | 110% | -2.6% | .card .big |
| typography/title/sheet | 20 / 600 | 129%（继承） | -2.1% | .sheet h3 |
| typography/body/default | 17 / 400 | 129% | -1% | body |
| typography/body/row | 17 / 400 | 125% | -1.2% | .lrow-t |
| typography/button/default | 17 / 600 | 129%（继承） | -1.2% | .btn |
| typography/body/list | 16 / 400 | 125% | -0.9% | .item .tt |
| typography/body/subtitle | 15 / 400 | 129%（继承） | -0.6% | .hdr .sub |
| typography/button/small | 15 / 600 | 129%（继承） | -1.2% | .btn.sm |
| typography/chip/default | 14 / 400 | 129%（继承） | -0.6% | .chip |
| typography/segmented/selected | 14 / 500 | 129%（继承） | -0.6% | .seg button.on |
| typography/caption/default | 13 / 400 | 138% | -1%（继承） | .small、.sect-f |
| typography/caption/row | 13 / 400 | 130% | -1%（继承） | .lrow-s |
| typography/section/title | 13 / 400 | 129%（继承） | -0.4% | .sect-t、.card h2 |
| typography/tag/default | 12 / 500 | 129%（继承） | -1%（继承） | .tag |
| typography/tab/label | 10 / 500 | 129%（继承） | .5% | #tabbar button |

caption/default 应用于 .small/.sect-f；其他 13px 文本有不同 tracking 或行高时不得因字号相同就自动套用。界面数值启用 tabular numerals；保留代码实际 400/500/600/700 权重。

## 5. Effects 与局部样式

Effect Styles 使用组件语义，非通用 elevation 1/2/3：

| 名称 | 参数（x y blur spread rgba） |
|---|---|
| effect/switch/knob | 0 3 8 0 rgba(0,0,0,.15) + 0 1 1 0 rgba(0,0,0,.16) |
| effect/segmented/selection | 0 3 8 0 rgba(0,0,0,.12) + 0 1 1 0 rgba(0,0,0,.06) |
| effect/slider/knob | 0 4 10 0 rgba(0,0,0,.22) + 0 1 2 0 rgba(0,0,0,.16) |
| effect/dialog/center | 0 20 60 -12 rgba(0,0,0,.50) |

tabbar 的背景模糊 10 + saturation 180%、scrim 模糊 2 记录为组件效果；Figma background blur 不能完整模拟 CSS saturation，注明近似。强调色中央按钮阴影、拖拽状态阴影、特殊进度渐变、动作图片、肌肉 SVG、照片保留在具体组件中，不提升为全局色板或全局阴影等级。

## 6. Master Components 候选与映射（阶段 B 预案）

全部采用 Auto Layout；尺寸、间距、颜色、文字分别绑定已确认的 Variables/Styles。只建立代码存在的变体。

| 组件 | 变体/属性 | 主要依赖 |
|---|---|---|
| button | appearance=primary/tinted/danger/plain/ghost；size=default/sm/xs；state=default/pressed/disabled；前后图标可选 | brand、surface、radius/control、button styles |
| icon-button | default/pressed/disabled；图标实例属性 | surface、36×36、radius/pill |
| text-field | default/focus；placeholder/value；multiline 单列组件 | surface、text、radius/control、focus 描边 |
| search-field | empty/filled；clear 可选 | surface/secondary、radius/10 |
| switch | off/on × default/pressed/disabled | brand、surface/tertiary、effect/switch/knob |
| segmented | selected/unselected item；default/pressed | surface、segmented text、effect/segmented/selection |
| stepper | default/invalid/disabled；数字与单位可编辑 | surface、danger、border、17px/500 |
| slider | default/dragging/disabled 交互注释 | brand、surface/tertiary、effect/slider/knob |
| check | off/on × default/pressed | brand、text/quaternary、30×30 |
| grouped-row | default/pressed/danger；icon/subtitle/value/accessory 可选 | text、surface、spacing/row-gap、border/hairline |
| card / section | 标题、内容 slot；分组行分隔 | surface、radius/card、spacing/card |
| chip / tag | chip default/selected/pressed；tag default/accent | brand、surface、radius/pill 或 radius/6 |
| tabbar | home/plan/start/stats/library；running 恢复状态 | brand、text、overlay、10px 标签 |
| sheet / center-dialog | 标题、内容、底部安全区说明 | bg 语义、radius/sheet 或 radius/16 |

disabled 在某些组件只存在行为限制而无独立视觉样式时，保留说明，不虚构 disabled 颜色。Keyboard focus 与 native press 不生成无证据的 hover 效果。

## 7. 已确认实施范围与后续步骤

1. 同意保留源码数值及语义差异，以代码提炼为准，不同时做视觉改版。
2. 第一阶段建立默认 lime 的 Light/Dark Tokens、上述 Text Styles 与 Effect Styles；其他强调色与 custom 先记录。
3. 检查字体后，SF Pro 不可用时允许使用 Inter + Noto Sans SC，并明确替代关系；这不是像素级系统字体还原。
4. 阶段 A 确认后只实施基础资产；阶段 B 组件候选清单已经具体化，创建 Master Components 仍按技能要求另行确认。

确认后先检查 Figma 实际变量/样式，按同名资产 KEEP/ADD/UPDATE 合并，记录返回 ID；不重建已有说明板、不擅自删除冲突资产。
