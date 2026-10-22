# Exercio 阶段 4 · 业务流装配资产明细（待确认）

上一阶段：阶段 3 已完成，样板 `App_Practice_Default` = `75:3`，页面 `03_Pilot` = `75:2`。35 个组件实例引用有效；无图片、字体错配、意外溢出；练习 84 BPM 与节拍器 120 BPM 独立。新增 Practice/Header、Practice/SessionContext、Practice/TransportButton 已回流组件库，共 47 个组件家族（含 Icon）。

代码基线：SamDozer/exercio，commit `3fc5194214588921ace19f9ff881f8ea3b1bc2a7`。范围沿用阶段 1 已确认的 Android 离线吉他练习产品，英文 UI，Studio Dark，390×844。

## 交付口径与装配约束

- 主页面共 18 个：训练计划 2、DNA 10、练习 3、历史 2、设置 1。本次新建其余 17 个，已有练习样板原位移入 `04_Screens`，保留 `75:3`；状态画板与弹层单独计数。
- 按下表每行生成命名资产；同一行列出的变体分别交付。弹层按内容 Hug，宽度受 390px 视口约束；全屏状态仍为 390×844。
- 组件只实例化已有 `02_Components` Master；容器 Auto Layout，文本可编辑，绑定已确认 Variables/Text Styles。缺组件先回流阶段 2/3；不临时添加新设计语言。
- 页面采用顶部栏、内容滚动区、底部操作区；执行页按样板保留 24px Android 上下留白与固定控制栏。底部三 Tab 仅顶层 Practice/History/Settings 可见。
- 默认、空、加载、错误按真实代码考虑；纯本地确定性问卷/展示不强造四态。异步失败按本地读写、音频、权限区分，具体见覆盖表。
- 主题对照只增加已确认的另外 5 个主题的练习页，对照现有 Studio Dark。不是 18 页乘 6；主题样例不作为重复业务流程本源。
- 原型采用 On click → Navigate/Open overlay/Close overlay/Back；页面过渡默认 Instant，弹层采用 Move in/溶解等与组件类型相符的过渡。不模拟真实持久化、实时计时与任意文本输入；提供有限的可点击代表状态。

## 流程模块一：训练计划与练习库

业务目标：创建计划、组织段落与练习、进入会话。2 个主页面，配套弹层与状态。

| 画板资产名称 | 装配 Master Component | Props / Overrides 覆盖 | 状态资产说明 | 交互连线动作 |
| :--- | :--- | :--- | :--- | :--- |
| `App_Routines_List` | Nav/TopBar、Card/Routine、Button/Action、Nav/BottomBar | Routines；Modern Fusion Practice；Practice Tab | 默认有计划 | 卡片编辑 → Editor；播放 → Overview；创建 → CreateChoice；切 Tab → History/Settings |
| `App_Routines_List_Empty/Loading/Error` | Status/Empty、Status/Message、Button/Action、Nav/BottomBar | 无计划创建入口；本地加载；Could not load routines | 分别对应真实空记录、加载、读取失败 | 创建 → CreateChoice；Retry → Default |
| `App_Routines_CreateChoice` | Overlay/Sheet、Input/ChoiceRow | Build my routine / Create manually | 默认选择弹层 | DNA → Q01；Manual → NameDialog；关闭回 List |
| `App_Routines_NameDialog_Create/Rename/Invalid` | Overlay/Dialog、Input/Field、Button/Action | Routine name；Name；空名称校验 | 创建、重命名、校验错误 | 保存 → Editor 或来源列表；取消关闭 |
| `App_Routine_Editor` | Nav/TopBar、Card/Section、Item/Exercise、Layout/TotalBar、Button/Action | 5 个练习段落、84 BPM、目标分钟数 | 默认编辑 | 添加 → Library；条目 → Slot；段落 → SectionDialog；开始 → Overview |
| `App_Routine_Editor_Empty/Loading/Missing` | Status/Empty、Status/Message、Button/Action | 无段落；加载；无法读取或计划不存在 | 无段落时开始禁用 | 添加 → Library；返回 List |
| `App_Library_Picker_Default/Expanded/Search/NoResults/Loading` | Overlay/Sheet、Input/Field、Item/Library、Chip/Choice、Status/Message | 类别/主题；推荐、最近练习；Search；自定义名称 | 15 类别、147 主题沿用代码；默认概览+代表类别展开；最近记录加载与无结果 | 类别展开；搜索 → Search/NoResults；主题 → PlacementChoice；已有练习 → Editor |
| `App_Library_Picker_NoRecent/Custom` | Overlay/Sheet、Input/Field、Item/Library、Button/Action | 无最近记录但保留推荐；自定义练习输入 | 最近读取失败降级为空；搜索失败归无结果，不造致命网络错误页 | 选择推荐或创建自定义 → Editor |
| `App_Library_PlacementChoice_WithSections/NoSections` | Overlay/Sheet、Input/ChoiceRow | 现有段落 / 新建段落 | 有/无段落选择差异 | 现有 → Editor；新建 → SectionDialog |
| `App_Exercise_Slot_Default/Invalid` | Overlay/Sheet、Input/Field、Input/BpmStepper、Input/SwitchRow、Chip/Choice、Button/Action | BPM、Focus 时长、调弦；提速起点/目标/步长/Clean 连胜 | 默认继承、覆盖值、提速参数校验错误 | 保存 → Editor；取消关闭；开关 → 展开对应字段 |
| `App_Routine_SectionDialog_Rename/Minutes/Invalid` | Overlay/Dialog、Input/Field、Button/Action | 段落名、分钟数、校验文案 | 名称/时长表单与错误变体 | 保存或取消 → Editor |
| `App_Routine_DeleteConfirm_Routine/Section`；`App_Routine_RemoveUndo` | Overlay/Dialog、Status/Snackbar、Button/Action | 删除对象与确认动作；Undo | 破坏性确认；移除练习后的撤销提示 | 取消关闭；删除 → 更新来源；Undo → 恢复 Editor |

## 流程模块二：Guitar DNA 与生成草案

业务目标：8 步答案 → 画像 → 可编辑的计划草案 → 保存。10 个主页面。

| 画板资产名称 | 装配 Master Component | Props / Overrides 覆盖 | 状态资产说明 | 交互连线动作 |
| :--- | :--- | :--- | :--- | :--- |
| `App_DNA_Q01_Mission` | DNA/QuestionHeader、DNA/ChoiceTile、Layout/StickyActions | 1/8；三年后的演奏目标；Continue | 未选禁用、已选 | 选择启用 Continue → Q02；Back 返回来源 |
| `App_DNA_Q02_Inspirations` | DNA/QuestionHeader、Input/Field、Chip/Choice、Layout/StickyActions | 2/8；榜样吉他手；搜索与自定义 | 未选可继续；搜索/自定义/已选容器 | Continue → Q03；Back → Q01 |
| `App_DNA_Q03_Traits` | DNA/QuestionHeader、DNA/ChoiceTile、Layout/StickyActions | 3/8；喜欢榜样哪些特质 | 已选；无榜样时适配解释文案 | Continue → Q04；Back → Q02 |
| `App_DNA_Q04_Time` | DNA/QuestionHeader、Input/Slider、Chip/Choice、Layout/StickyActions | 4/8；单次时长、每周天数 | 有效默认值与调整值 | Continue → Q05；Back → Q03 |
| `App_DNA_Q05_Experience` | DNA/QuestionHeader、DNA/ChoiceTile、Layout/StickyActions | 5/8；Where are you right now? | 未选禁用、已选 | Continue → Q06；Back → Q04 |
| `App_DNA_Q06_Weakness` | DNA/QuestionHeader、DNA/ChoiceTile、Layout/StickyActions | 6/8；当前练习痛点 | 未选禁用、已选 | Continue → Q07；Back → Q05 |
| `App_DNA_Q07_Styles` | DNA/QuestionHeader、Chip/Choice、Layout/StickyActions | 7/8；偏好的音乐风格 | 未选可继续、多选 | Continue → Q08；Back → Q06 |
| `App_DNA_Q08_Dream` | DNA/QuestionHeader、DNA/ChoiceTile、Layout/StickyActions | 8/8；最想提升的能力；最终提交 | 未选禁用、已选 | 提交成功 → Result；Back → Q07 |
| `App_DNA_Result` | Nav/TopBar、DNA/ResultCard、Button/Action | Practice path、8 维雷达与等价文本、画像来源 | 本地确定性结果，无网络加载页 | 生成 → Draft；修改答案 → 带答案 Q01 |
| `App_DNA_Draft` | Nav/TopBar、DNA/DraftSection、Layout/TotalBar、Button/Action | Your routine、名称、段落、分钟、主题 | 默认草案 | 保存 → List；编辑 → DraftAdjust；Regenerate → 默认草案 |
| `App_DNA_Draft_Empty/Saving/SaveFailed` | Status/Empty、Status/Message、Status/Snackbar、Button/Action | 无段落添加；保存中禁用；Could not save this routine | 空草案、保存中、真实失败提示 | 添加 → Category；失败保留草案可重试；成功 → List |
| `App_DNA_DraftAdjust_Category/Topics/Minutes/Why/Name` | Overlay/Sheet、Overlay/Dialog、Input/ChoiceRow、Chip/Choice、Input/Field、Button/Action | Change/Add section；主题多选；分钟；Why；Routine name | 选择、无可添加类别、名称/分钟校验 | 确认应用 → Draft；Cancel/Close 关闭 |

问卷状态附页：Q01/Q05/Q06/Q08 的 Selected；Q02 Search/Custom；Q03 NoInspirations；Q04 Adjusted；Q07 Selected。未选默认页不重复。DNA 答案持久化失败虽由控制器记录 error，但当前流程没有统一消费该 error 的独立错误界面；只在说明中标注实现缺口，不虚构已实现的失败弹窗。

## 流程模块三：练习执行、Focus 与节拍器

业务目标：开始会话 → 独立 Focus 尝试 → 评分 → 保存总结。主页面 3 个，其中执行样板已存在。

| 画板资产名称 | 装配 Master Component | Props / Overrides 覆盖 | 状态资产说明 | 交互连线动作 |
| :--- | :--- | :--- | :--- | :--- |
| `App_Session_Overview` | Nav/TopBar、Card/Section、Input/ChoiceRow、Button/Action | Before you start；计划段落、目标、切段模式 | 无段落在入口拦截；此页无需异步四态 | Start session → Default；Back → 来源 |
| `App_Practice_Default`（已有 75:3） | Practice/Header、SectionStrip、Timer、SessionContext、Item/Exercise、Input/BpmStepper、Practice/Metronome、Transport | Modern Fusion Practice；Warm-up；02:18/5 min；84 BPM；节拍器120 BPM | 首段 Previous 禁用；保留样板 ID | Start Focus → Focus；Pause → Paused；Next → 下一段代表状态；End → EndConfirm |
| `App_Practice_Focus/FocusOverrun` | Practice/FocusPanel、Practice/Timer、Practice/Transport、Practice/Metronome | 当前练习与独立 Focus 计时；Finish Focus | 进行中/超时；练习选择和速度按代码锁定 | Finish → Rating；End → EndConfirm |
| `App_Practice_Rating/RatingSelected/RatingMinimal` | Practice/RatingPanel、Button/Action | Clean/Shaky/Failed；可选质量/疲劳 | 未评、已评、设置隐藏可选字段 | 评分 → Selected；继续 → Default |
| `App_Practice_Paused/SectionNext/SectionLast/Overrun` | Practice/Timer、SectionStrip、SessionContext、Transport | Resume；下一段；末段 Next 禁用；目标超时 | 暂停、切段与边界、超时 | Resume → Default；Previous/Next → 对应段落；结束 → EndConfirm |
| `App_Practice_Simple` | Practice/Header、Timer、SessionContext、Transport、Status/Message | 段落无练习时保留正常计时与说明 | 合法无练习模式，不显示 Focus 表单 | 切段或结束 |
| `App_Practice_Insight/InsightNoHistory` | Practice/Insight、Item/Exercise | 当前/目标速度、难度、Clean streak、曲线、标签与参考 | 有数据、无曲线历史；按设置显示字段 | 返回 Default；开始 Focus |
| `App_Practice_MetronomeStarting/On/Failed/Locked` | Practice/Metronome | Starting/On/Failed/Locked；独立120 BPM | 音频初始化/启用/失败/Focus锁定 | On/Off 切换；失败重试；结束 Focus 解锁 |
| `App_Practice_EndConfirm` | Overlay/Dialog、Button/Action | End session；结束不可恢复提示 | 取消/确认 | Cancel 关闭回来源状态；End → Finishing |
| `App_Practice_Finishing/EndFailed` | Practice/Transport、Practice/Metronome、Status/Message、Button/Action | 保存中禁用操作；本地保存失败并保留会话 | Loading/Error | 成功 → Summary；Retry → Finishing |
| `App_Session_Summary` | Nav/TopBar、History/SummaryStats、Button/Action | 实际时长、暂停、段落、尝试次数；Done | 确定性总结；无尝试仍可有合法会话 | Done → List；History Tab 可查看 Review |
| `App_Practice_Theme_PrismEcho/AlienVelocity/HandmadeDream/PracticeCartridge/MementoChrome` | 同样板组合，切换已建主题模式与文字样式 | 相同练习数据；各主题进度视觉 | 5 个主题对照，无新增业务主页面 | 演示入口定位主题对照；Back 回主流程 |

## 流程模块四：历史与复盘

业务目标：浏览已保存会话，修改笔记、尝试和观察。2 个主页面。

| 画板资产名称 | 装配 Master Component | Props / Overrides 覆盖 | 状态资产说明 | 交互连线动作 |
| :--- | :--- | :--- | :--- | :--- |
| `App_History_List` | Nav/TopBar、History/SummaryStats、History/SessionCard、Nav/BottomBar | History；总览指标；Modern Fusion Practice | 默认数据 | 会话 → Review；Tab → List/Settings |
| `App_History_List_Empty/Loading/Error` | Status/Empty、Status/Message、Button/Action、Nav/BottomBar | No sessions；加载；Could not load history | 本地空数据、加载、失败 | 空态去练习；失败 Retry → List |
| `App_History_Review` | Nav/TopBar、History/SummaryStats、History/Attempt、History/ReviewNote | 段落、Focus 尝试、评分、提速结果、会话笔记、观察 | 默认；无尝试段落仍保留记录 | 编辑 → EditNote/EditAttempt/Observation；Back → History |
| `App_History_Review_Loading/Missing/OperationError` | Status/Message、Button/Action、Status/Snackbar | 加载；会话不存在；操作失败提示 | 按真实读取/操作反馈表现 | 返回 History；允许的操作重试 |
| `App_History_EditNote/AttemptNote/Observation` | Overlay/Dialog、Input/Field、Button/Action | 多行会话笔记、尝试描述、新增/编辑观察 | 默认、编辑值 | Save/Cancel → Review |
| `App_History_EditAttemptRating` | Overlay/Sheet、Practice/RatingPanel、Button/Action | 已存尝试评分与修改 | 当前评分/已修订 | 保存 → Review；取消关闭 |
| `App_History_ObservationDelete` | Overlay/Dialog、Button/Action | 删除观察确认 | 确认/取消 | 删除 → Review；取消关闭 |

## 流程模块五：设置与个人偏好

业务目标：调整主题、显示字段、音色和会话偏好，管理 DNA。1 个长滚动主页面。

| 画板资产名称 | 装配 Master Component | Props / Overrides 覆盖 | 状态资产说明 | 交互连线动作 |
| :--- | :--- | :--- | :--- | :--- |
| `App_Settings_Main` | Nav/TopBar、Settings/Section、Settings/ThemeOption、Settings/SoundOption、Input/SwitchRow、Input/ChoiceRow、Settings/DNAProfile、Nav/BottomBar | 六主题、六组音色、手动/自动、后台提示、亮屏、DNA | 单一长滚动页，字段/音频/DNA 仅滚动定位，不伪造路由 | Tab → List/History；主题 → 对照样例；DNA → Result/Q01/Draft |
| `App_Settings_Loading/Error/DNAEmpty/RatingOff/PermissionDenied` | Status/Message、Input/SwitchRow、Settings/DNAProfile、Button/Action | 设置加载/读写错误；DNA未建档；关闭结果评分提示；权限状态 | 本地异步、选项依赖与系统权限 | 重试/切换；建立DNA → Q01；权限入口外链到 Android 系统说明 |
| `App_Settings_DNAReset` | Overlay/Dialog、Button/Action | Reset Guitar DNA；保留已存计划与历史 | 确认/取消；控制器 reset 失败仅标注代码反馈能力 | 确认 → DNAEmpty；取消关闭 |

## 四态真实性覆盖矩阵

| 核心页面/容器 | Default | Empty | Loading | Error |
| :--- | :--- | :--- | :--- | :--- |
| 计划列表 | 计划卡 | 无计划 | 本地读取 | 本地读取失败/创建反馈 |
| 计划编辑 | 段落与练习 | 无段落 | 本地读取 | 不存在/读取失败；输入错误在表单 |
| 练习库 | 推荐/最近/类别 | 无最近/无匹配 | 最近记录加载 | 降级到空最近或无结果；不造错误路由 |
| DNA 问卷 | 选项 | 未选/无榜样分支 | N/A 本地静态问卷 | 必填约束；保存 error 消费缺口仅说明 |
| DNA 结果 | 画像 | N/A 由答案生成 | N/A 本地算法 | N/A 不造远程错误 |
| DNA 草案 | 段落 | 全部移除 | 保存中 | 保存失败 Snackbar |
| 会话概览 | 有效计划 | 入口拦截无段落 | N/A | N/A |
| 练习执行 | 运行/暂停/Focus | 合法 Simple 模式 | 音频启动/结束保存 | 音频失败/结束保存失败 |
| 会话总结 | 已完成会话 | 零尝试仍合法 | N/A | N/A |
| 历史列表/复盘 | 历史/详情 | 无记录/无尝试 | 本地读取 | 读取失败/不存在/操作失败 |
| 设置 | 当前偏好 | DNA未建档 | 设置读取 | 设置读写失败；权限状态独立 |

## 原型闭环与验收

1. 手动创建：List → CreateChoice → NameDialog → Editor → Library → Placement/Slot → Overview → Practice → Focus → Rating → Practice → EndConfirm → Finishing → Summary → List。
2. DNA：List/Settings → Q01…Q08 → Result → Draft → 调整弹层 → 保存 → List → 新计划练习。
3. 复盘：History → Review → Note/Attempt/Observation → 保存或取消 → Review → History。
4. 偏好：Settings 长滚动 → 字段/音色/模式/DNA；主题对照入口；Tab 回练习。
5. 失败/恢复：列表 Retry、表单校验、音频重试、会话保存重试；所有弹层有取消/关闭，返回不跳往无关页面。
6. QA：记录全部节点 ID、组件引用、关键 Props、文字可编辑、Token/Style 绑定、滚动/固定栏/禁用点击与目标连线；截图抽查每个模块和关键状态，逐条验证闭环。主题切换核对字体与特殊进度呈现。

## 本轮确认范围

确认以上五模块的 17 个新增主页面、已有样板迁入、表列配套状态/弹层、5 个主题对照及原型连线。阶段 5/6 的画布与组件库整理另行列门禁，不包含在本轮执行中。
