# Exercio · 阶段 2B Master Components 制作清单（已确认并完成）

流水线进度：阶段 2 / 6 · figma-tokens-system · Components 门禁清单

上一部分已完成：201 个 Variables（142 原始值＋59 语义变量）、6 个主题模式、90 个 Text Styles、1 个 Effect Style，以及 01_Design Tokens 原生规范看板。

Figma规范看板：https://www.figma.com/design/g6O2q2Al59n0i1yR57gCZn/exercio?node-id=39-37

## 执行约定

- 组件页拟为 `02_Components`；本轮只出清单，尚未创建主组件。
- 共 **44 个组件家族**（下表每行一个家族；Icon/Material 是按实际使用图标创建的主组件集合，具体图标逐一来源于源码的 Material 图标）。
- 每个控件内部采用 Auto Layout，基础控件先构建，业务组合内使用实例；文本采用 Text properties、图标用 INSTANCE_SWAP、可选内容用 Boolean properties。
- 六主题颜色、间距、圆角和字号由变量模式控制，不给每个基础控件乘上六倍 Theme Variant。字体采用已确认的主题 Text Style；主题对照页显式切换相应文字样式，不能声称只切颜色模式就能完成所有字体变化。
- 默认主组件展示 Studio Dark；另外保留 Progress 六种风格以及六主题色板说明。交互样例绘制在组件展示范围，业务页交互在阶段 4 配置。
- 每家族变体轴控制在 30 种以内；按本表实际状态创建，不做所有 Boolean 与状态的笛卡尔积。
- Icon优先从可用Material库导入；源码是Flutter图标字体而非SVG时，使用对应Material原始SVG/兼容库矢量，保留来源记录，不用旋转线段重画图标。
- Pressed 是移动端按压反馈，静态页面默认展示 Default。Input 的 Focused/Error 与控件禁用反馈保持明确。
- 不增加新颜色或间距。样板出现未覆盖资产时先列回流明细，不擅自新造Token。

## UI 功能模块 1：导航与图标

| Master Component 建议名称 | 内部图层 Auto Layout 结构 | 绑定 Design Tokens 映射 | 预设 Variants 变体维度 | 来源参考图区域 |
| --- | --- | --- | --- | --- |
| `Icon/Material` | SVG原生矢量；固定24px图标槽 | color/text/primary、color/accent | 独立图标主组件；控件内用INSTANCE_SWAP，不以图标名扩展控件变体 | app_shell及各页Material Icons |
| `Nav/TopBar` | H：返回图标＋标题＋尾部操作 | color/bg/surface、heading/md、spacing/md、size/touch-min | Mode=Title/Back/Actions | 各screen AppBar |
| `Nav/Destination` | V：顶部选中标记＋图标＋标签 | color/state/selected、color/state/unselected、label/md、size/navigation-indicator | Selected=True/False；Label文本、Icon交换 | app_shell底部导航 |
| `Nav/BottomBar` | H：3个Destination实例 | color/bg/surface、spacing/xs | Active=Practice/History/Settings | app_shell底部导航 |

## UI 功能模块 2：基础操作与表单

| Master Component 建议名称 | 内部图层 Auto Layout 结构 | 绑定 Design Tokens 映射 | 预设 Variants 变体维度 | 来源参考图区域 |
| --- | --- | --- | --- | --- |
| `Button/Action` | H：可选图标＋可编辑标签；高度64或最小48 | color/action/*、color/bg/interactive、radius/control、label/lg、size/control-primary | Emphasis=Primary/Secondary/Destructive；State=Default/Pressed/Disabled（9）；HasIcon布尔 | routine_widgets、各页主按钮 |
| `Button/Icon` | H：图标交换槽，最小48触控区 | color/text/primary、color/text/disabled、radius/control、size/touch-min | State=Default/Pressed/Disabled；Selected布尔 | 创建、编辑、返回、菜单 |
| `Input/Field` | V：Label＋输入容器＋可选Helper | color/bg/elevated、color/border/default、color/state/focus、color/state/failure、radius/input、body/md | Kind=Text/Number/Multiline；State=Default/Focused/Error（9） | 命名、时长、BPM、笔记、搜索输入 |
| `Input/BpmStepper` | H：减按钮＋数字＋加按钮 | numeric/lg、spacing/md、size/touch-min | State=Enabled/Locked/Disabled；BPM文本 | Practice staged BPM、Metronome |
| `Input/Slider` | H：轨道＋已填充段＋Thumb | color/accent、color/state/unselected | State=Enabled/Disabled；Value文本/位置覆盖 | Metronome与分钟调整 |
| `Input/SwitchRow` | H：V(Label＋说明)＋开关 | body/md、body/sm、color/text/*、spacing/sm | On=True/False；Enabled=True/False（4） | Settings字段、亮屏、渐进提速 |
| `Input/ChoiceRow` | H：V(Label＋说明)＋单选/多选标记 | color/bg/surface、color/state/selected、stroke/selected、body/md | Kind=Radio/Checkbox；Selected=True/False（4） | 切段、音色、主题/话题选项 |
| `Chip/Choice` | H：可选图标＋标签 | color/bg/interactive、color/state/selected、radius/control、label/md | Selected=True/False；Removable布尔 | 风格、调弦、标签、DNA选项 |

## UI 功能模块 3：卡片与练习内容

| Master Component 建议名称 | 内部图层 Auto Layout 结构 | 绑定 Design Tokens 映射 | 预设 Variants 变体维度 | 来源参考图区域 |
| --- | --- | --- | --- | --- |
| `Card/Surface` | V：可嵌套内容容器 | color/bg/surface、color/bg/elevated、color/bg/interactive、radius/card、stroke/*、spacing/md | Tone=Default/Elevated/Selected | ThemedCard与主题ComponentTokens |
| `Card/Routine` | V：标题/统计＋播放与菜单 | Card/Surface、heading/sm、body/sm、spacing/md | State=Default；Name/Sections/Duration文本 | routine_list_screen |
| `Card/Section` | V：H(名称/时长/菜单)＋练习实例列表 | Card/Surface、heading/sm、spacing/md、spacing/sm | Content=WithExercises/Empty；Edit布尔 | routine_editor_screen |
| `Item/Exercise` | H：V(名称＋BPM/时长)＋状态图标 | color/bg/surface、color/bg/interactive、color/state/selected、radius/control、stroke/*、body/lg | Selected=True/False；Disabled布尔 | Editor、Practice练习选择 |
| `Item/Library` | H：图标＋V(名称/说明)＋尾部操作 | body/lg、body/sm、spacing/sm、size/touch-min | Kind=Category/Topic/Recent/Custom | catalog_picker |

## UI 功能模块 4：弹层与反馈

| Master Component 建议名称 | 内部图层 Auto Layout 结构 | 绑定 Design Tokens 映射 | 预设 Variants 变体维度 | 来源参考图区域 |
| --- | --- | --- | --- | --- |
| `Overlay/Sheet` | V：标题/关闭＋内容槽＋可选底部操作 | color/bg/elevated、radius/card、spacing/md | ActionBar=True/False；标题文本 | CreateChoice、Library、Slot、Draft调整 |
| `Overlay/Dialog` | V：标题＋说明/输入槽＋H按钮组 | color/bg/elevated、radius/card、spacing/md、heading/sm | Kind=Confirm/Text/Number；Destructive布尔 | 结束、删除、命名、分钟、Why说明 |
| `Status/Message` | H或V：图标＋说明＋可选重试 | color/state/failure、color/state/warning、color/state/neutral、body/md | State=Info/Warning/Error/Loading；HasAction布尔 | 加载、本地读写失败、锁定提示 |
| `Status/Empty` | V：图标＋标题＋说明＋操作按钮 | heading/sm、body/md、spacing/md、Button/Action | Kind=Routines/Sections/History/Search；CTA布尔 | EmptyState、目录无结果 |
| `Status/Snackbar` | H：消息＋可选Undo | color/bg/elevated、body/md、label/lg、spacing/md | Action=None/Undo | 移除练习、保存反馈 |

## UI 功能模块 5：Guitar DNA

| Master Component 建议名称 | 内部图层 Auto Layout 结构 | 绑定 Design Tokens 映射 | 预设 Variants 变体维度 | 来源参考图区域 |
| --- | --- | --- | --- | --- |
| `DNA/QuestionHeader` | V：H(标题/步骤)＋进度＋问题＋说明 | heading/md、display/md或heading/lg、body/md、spacing/md | Progress文本/数值覆盖；HasSubtitle布尔 | dna_onboarding、DnaStepHeader |
| `DNA/ChoiceTile` | H：V(标题/说明)＋选中标记 | color/bg/surface、color/bg/interactive、color/state/selected、stroke/*、radius/card | Selected=True/False；HasDescription布尔 | DnaChoiceTile |
| `DNA/ResultCard` | V：路径名＋说明＋可编辑矢量雷达＋来源/优先级 | Card/Surface、heading/lg、body/md、color/accent、spacing/md | HasInspirations=True/False；文字/雷达数值覆盖 | dna_result_screen、dna_radar_chart |
| `DNA/DraftSection` | V：段落头＋分钟＋话题＋理由＋菜单 | Card/Surface、heading/sm、body/sm、Chip/Choice | Topics=Present/Empty；Reason=Present/Manual（4） | routine_draft_screen |

## UI 功能模块 6：计时、节拍器与练习

| Master Component 建议名称 | 内部图层 Auto Layout 结构 | 绑定 Design Tokens 映射 | 预设 Variants 变体维度 | 来源参考图区域 |
| --- | --- | --- | --- | --- |
| `Practice/SectionStrip` | H：段落标签实例，可水平滚动 | color/state/selected、color/state/unselected、radius/control、stroke/*、label/lg | 选中项通过实例属性；段落名文本覆盖 | practice_screen _SectionStrip |
| `Practice/Timer` | V：主时间＋进度＋目标/超时说明 | timer/lg、color/bg/surface、radius/card、spacing/lg、spacing/md | Kind=Section/Focus；State=Running/Paused/Overrun（6） | TimerArea、FocusPanel |
| `Practice/Progress` | H或原生矢量：轨道＋当前进度 | color/state/unselected、color/action/primary、color/accent、color/action/secondary、size/progress-thickness | Style=Linear/Spectral/Energy/Arc/Pixel/Chrome（6）；Value覆盖 | themed_progress；含6主题对照所需风格 |
| `Practice/FocusPanel` | V：当前练习＋Focus计时器实例＋结束按钮 | Practice/Timer、heading/sm、Button/Action、spacing/md | State=Active/Overrun；Exercise文本 | practice_screen _FocusPanel |
| `Practice/RatingPanel` | V：结果选择＋可选质量/疲劳刻度 | color/state/success、warning、failure、neutral、label/lg、spacing/sm | Outcome=Unrated/Clean/Shaky/Failed（4）；Quality/Fatigue布尔；分值文本 | _RatingPrompt、_RatingScale |
| `Practice/Transport` | V：H(Previous/Pause或Resume/Next)＋End | Button/Action、Button/Icon、color/bg/surface、spacing/sm | Paused=True/False；PreviousEnabled/NextEnabled布尔 | _Transport |
| `Practice/Metronome` | V：头部状态＋BPM＋Stepper/Slider＋节拍与开关 | Card/Surface、numeric/lg、label/lg、Input/*、color/accent | State=Off/Starting/On/Failed/Locked/Finishing（6） | metronome_panel；Focus锁定时不能改BPM |
| `Practice/Insight` | V：统计行＋原生矢量Sparkline＋标签/参考说明 | numeric/md、body/sm、color/state/success、color/accent、spacing/sm | History=Present/Absent；图/标签/难度等布尔 | exercise_info_panel、ProgressSparkline |

## UI 功能模块 7：历史与复盘

| Master Component 建议名称 | 内部图层 Auto Layout 结构 | 绑定 Design Tokens 映射 | 预设 Variants 变体维度 | 来源参考图区域 |
| --- | --- | --- | --- | --- |
| `History/SessionCard` | V：日期/计划名＋统计摘要 | Card/Surface、heading/sm、body/sm | Default；所有显示值文本覆盖 | history_screen _SessionCard |
| `History/SummaryStats` | H或Wrap：指标项实例(数值＋标签) | numeric/md、label/md、spacing/md | Layout=Row/Wrap；指标显示布尔 | History totals、SessionSummary、Review summary |
| `History/Attempt` | V：练习名＋时长/BPM＋结果选择＋笔记入口 | Item/Exercise、Practice/RatingPanel的结果子组件、body/sm | Outcome=Unrated/Clean/Shaky/Failed；Note布尔 | session_review_screen _AttemptTile |
| `History/ReviewNote` | V：标题＋内容＋编辑/删除入口 | Card/Surface、heading/sm、body/md、color/state/success | Kind=Note/Observation/Progression（3）；Empty布尔 | NoteCard、ObservationCard、ProgressionCard |

## UI 功能模块 8：偏好设置

| Master Component 建议名称 | 内部图层 Auto Layout 结构 | 绑定 Design Tokens 映射 | 预设 Variants 变体维度 | 来源参考图区域 |
| --- | --- | --- | --- | --- |
| `Settings/ThemeOption` | H：颜色预览＋V(主题名/描述)＋选择标记 | color/bg/*、color/accent、Input/ChoiceRow、body/md | Selected=True/False；六个主题名称/色板覆盖 | _ThemeOption、_ThemeSwatch |
| `Settings/SoundOption` | H：V(音色名/说明)＋预览/单选 | Input/ChoiceRow、Button/Icon、body/md | Selected=True/False；Playing布尔 | _SoundPackPicker |
| `Settings/Section` | V：标题＋SettingsRow实例列表 | heading/sm、body/md、spacing/md、Card/Surface | Kind=Fields/Practice/Permission；说明布尔 | SettingsSection、BackgroundCueRow、亮屏；权限用Choice/Status组合 |
| `Settings/DNAProfile` | V：未建档说明或路径摘要＋动作组 | Card/Surface、Button/Action、body/md | Profile=Absent/Present；Loading布尔 | guitar_dna_settings |

## UI 功能模块 9：布局与组合框架

| Master Component 建议名称 | 内部图层 Auto Layout 结构 | 绑定 Design Tokens 映射 | 预设 Variants 变体维度 | 来源参考图区域 |
| --- | --- | --- | --- | --- |
| `Layout/StickyActions` | H或V：底部主/次操作实例，独立安全区 | color/bg/surface、spacing/md、size/control-primary | Layout=Single/Pair/Transport；SafeArea可调 | 问卷Footer、底部保存、练习控制 |
| `Layout/TotalBar` | H：总时长说明/数值＋可选操作 | body/md、numeric/md、color/bg/surface、spacing/md | Action=True/False；Total文本 | RoutineEditor、RoutineDraft总时长 |

## 依赖与构建顺序

1. 图标、Button、Input、Chip、Card Surface。
2. 导航、弹层、反馈、布局骨架。
3. 计划、目录、DNA、计时/节拍器、历史与设置的业务组合。
4. 覆盖每家族关键变体，检查实例引用、文本属性、图标交换、Token绑定、可编辑性、字体和排版。

## 验收与交接

- 所有创建/更新的主组件及Variant返回稳定node ID，记录在本地ledger；后续页面全部优先用实例装配。
- `02_Components` 包含可读名称、关键状态、用途说明和来源；九家族的最终溯源/紧凑矩阵仍留在阶段6整理。
- 本轮不创建18个业务主画板。组件完成后进入阶段3，先提交 `App_Practice_Default` 的垂直模块样板清单。
- 本轮请确认：**阶段2 Components全部清单**，或点名需要调整的组件家族。

门禁依据：用户指定的 figma-tokens-system 明确要求“在进入02_Components页面绘制前，必须按组件功能模块输出制作明细表，并停止等待用户确认”。

## 执行结果

已完成44家族：39个Component Sets、4个单体业务组件与32图标构成的Icon家族；共165个Component节点。所有图标原生矢量、无整页图片。组件总览node ID：51:592。最终检查无溢出、无无效实例引用，Chip/Slider触控区48px。

实际属性适配：Transport的ShowPrevious/ShowNext只表示可见性；禁用使用嵌套Button的State=Disabled，以免将隐藏当作禁用。雷达图为八维可编辑矢量，示例值用于视觉展示，不运行DNA计算。
