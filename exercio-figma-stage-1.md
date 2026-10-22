# Exercio · 阶段 1 模块画板资产明细（已确认）

流水线进度：阶段 1 / 6 · figma-prd-architect · 已确认。用户确认五模块全部清单，后续按本清单交接。

目标 Figma：https://www.figma.com/design/g6O2q2Al59n0i1yR57gCZn/exercio

## 代码依据与边界

- GitHub：`SamDozer/exercio`；本地路径：`/Users/haizei/work/AI/github/吉他优秀开源库/exercio`。
- 分析基线：`3fc5194214588921ace19f9ff881f8ea3b1bc2a7`，2026-08-01；本次未修改源码。未校验远端 HEAD 与本地是否一致，因此本清单明确绑定此提交。
- 产品：面向吉他练习者的离线 Android 应用，无账号、无服务器。唯一业务角色是本机练习者。
- 结构：Flutter 应用负责界面；practice_core 负责领域和练习状态；practice_data 负责本地存储；practice_audio_api / practice_audio_android 负责节拍器与提示音。
- 顶层导航：Practice / History / Settings。练习库属于编辑时的选择弹层，进度图属于练习信息面板，不增加独立导航。
- 本轮需求依据为已实现源码；分享、云同步、登录、iOS/Web、调音器、录音、伴奏、提醒和社区均属于未纳入本轮的路线图能力。
- 原型文案建议沿用源码英文；说明看板用中文。整套主流程建议采用默认 Studio Dark。其余五主题保留为规范模式与同一练习状态的主题对照，不复制全部业务页面。
- 已发现仓库视觉证据：`docs/screenshots/01-home.png` 至 `10-settings.png`，以及 `docs/screenshots/themes/` 下六主题截图；已查看练习页截图。
- 尚未读取或修改目标 Figma；遵守阶段 1 的确认门禁。

## 模块树

```text
Exercio
├─ Practice（顶层导航）
│  ├─ 训练计划列表：创建、重命名、删除、开始练习
│  ├─ 手动编辑：段落、时长、练习项目、单次位置参数、渐进提速策略
│  ├─ 练习库：类别、推荐主题、最近练习、搜索、自定义名称
│  ├─ Guitar DNA：八步问卷 → 结果 → 可编辑草案 → 保存计划
│  └─ 实际练习：开始前概览 → 段落计时 / Focus / 节拍器 → 会话总结
├─ History（顶层导航）
│  └─ 历史列表 → 会话复盘 → 修订评分、笔记与观察
└─ Settings（顶层导航）
   ├─ 六套视觉主题、六组节拍器音色
   ├─ 练习字段显示、评分选项、手动 / 自动切段
   ├─ 后台提示音系统权限、保持亮屏
   └─ DNA 资料查看、修改、重新生成、重置
```

## 模块一：训练计划与练习库

- **业务职责**：把可复用练习组成带目标时长的训练计划，并进入练习。
- **涉及用户角色**：本机练习者。
- **端侧归属**：Android 移动端。

| 画板/页面资产建议命名 | 优先级 | 页面类型 | 核心内容与结构模块 | 装配组件资产需求 (02_Components) | 状态资产覆盖 (Default/Empty/Loading/Error) | 交互流转去向 |
| --- | --- | --- | --- | --- | --- | --- |
| `App_Routines_List` | P0 | 高频入口 | Routines 标题、DNA 入口、计划卡、创建按钮、底部导航 | AppHeader、BottomNav、RoutineCard、PrimaryButton、EmptyState | 默认、无计划、加载、本地读取失败及重试 | 编辑 → Editor；播放 → Overview；创建 → CreateChoice |
| `App_Routines_CreateChoice` | P0 | 底部弹层 | Build my routine / Create manually | BottomSheet、ActionRow | 默认、DNA 功能入口可用性按控制器状态 | DNA_Q01 或命名对话框 → Editor |
| `App_Routines_NameDialog` | P0 | 对话框 | 新建或重命名计划，共用输入组件 | TextDialog、TextField、Button | 默认、空名称校验、保存反馈 | 保存 → List / Editor；取消 → 来源页 |
| `App_Routine_Editor` | P0 | 编辑页 | 计划名、段落卡、段落时长、练习条目、总时长、开始练习 | SectionCard、ExerciseRow、TotalBar、ActionMenu、PrimaryButton | 默认、无段落、加载、计划不存在；无段落不能开始 | 添加 → Library；练习条目 → Slot；播放 → Overview |
| `App_Library_Picker` | P0 | 底部弹层 | 15 类别、147 主题；推荐和最近练习；搜索；自定义名称 | SearchField、CategoryRow、TopicRow、RecentExerciseRow、BottomSheet | 类别列表、类别展开、搜索结果、无匹配、自定义输入；加载/错误按数据路径复核 | 选择主题 → PlacementChoice；选择已有练习 → Editor |
| `App_Library_PlacementChoice` | P1 | 底部弹层 | 主题放入现有段落或新建段落 | BottomSheet、ActionRow | 无现有段落 / 有现有段落 | 选择后 → Editor；新段落可能继续填写时长 |
| `App_Exercise_Slot` | P0 | 底部表单 | 此位置的 BPM、Focus 目标、调弦；提速起点、目标、步长、连续 Clean 次数 | NumericField、TuningChip、Switch、ProgressionForm | 默认、未覆盖默认值、参数校验错误、保存中 | 保存 → Editor |
| `App_Routine_SectionDialog` | P1 | 对话框变体 | 段落重命名、设置分钟数 | TextDialog、NumericField | 默认、非法名称 / 时长 | 确认 → Editor |
| `App_Routine_DeleteConfirm` | P1 | 对话框变体 | 删除计划 / 段落的确认；练习移除后的 Undo 提示 | ConfirmDialog、Snackbar | 取消、确认、移除后撤销 | 返回来源页并刷新 |

代码：`features/routines/routine_list_screen.dart`、`routine_editor_screen.dart`、`catalog_picker.dart`、`slot_editor_sheet.dart`。

## 模块二：Guitar DNA 与生成草案

- **业务职责**：以本地确定性问卷生成练习优先级和可编辑训练计划。
- **涉及用户角色**：首次建档或调整练习目标的本机练习者。
- **端侧归属**：Android 移动端。

| 画板/页面资产建议命名 | 优先级 | 页面类型 | 核心内容与结构模块 | 装配组件资产需求 (02_Components) | 状态资产覆盖 (Default/Empty/Loading/Error) | 交互流转去向 |
| --- | --- | --- | --- | --- | --- | --- |
| `App_DNA_Q01_Mission` | P0 | 问卷步骤 | 三年后的演奏目标、单选、1/8、进度条 | QuestionHeader、ChoiceTile、StepProgress、StickyFooter | 未选、已选、Continue 禁用 | Q02；返回取消 |
| `App_DNA_Q02_Inspirations` | P0 | 问卷步骤 | 吉他手搜索、预置项、自定义名字 | SearchField、ChoiceChip、CustomEntry | 未选可继续、搜索、已选、自定义 | Q03 / Q01 |
| `App_DNA_Q03_Traits` | P0 | 问卷步骤 | 对榜样演奏特质的偏好 | ChoiceTile、QuestionHeader | 无榜样时适配内容；未选 / 已选 | Q04 / Q02 |
| `App_DNA_Q04_Time` | P0 | 问卷步骤 | 可实践的单次时长、每周天数 | DurationControl、ChoiceChip | 默认值、调整后 | Q05 / Q03 |
| `App_DNA_Q05_Experience` | P0 | 问卷步骤 | 当前经验单选 | ChoiceTile | 未选禁用、已选 | Q06 / Q04 |
| `App_DNA_Q06_Weakness` | P0 | 问卷步骤 | 当前最大练习痛点 | ChoiceTile | 未选禁用、已选 | Q07 / Q05 |
| `App_DNA_Q07_Styles` | P0 | 问卷步骤 | 偏好音乐风格 | ChoiceChip | 未选可继续、已选 | Q08 / Q06 |
| `App_DNA_Q08_Dream` | P0 | 问卷步骤 | 最想提升的能力、最终提交 | ChoiceTile、PrimaryButton | 未选禁用、已选；持久化失败需复核反馈 | Result / Q07 |
| `App_DNA_Result` | P0 | 结果页 | Practice path、说明、雷达图、来源、生成按钮、修改答案 | DNAResultCard、RadarChart、SourceChip、PrimaryButton | 默认、资料信息差异；不伪造网络加载 | Draft；修改 → Q01（带原答案） |
| `App_DNA_Draft` | P0 | 生成后编辑 | 名称、总时长、段落、主题、生成理由、调整与保存 | DraftSectionCard、TotalBar、ActionMenu、PrimaryButton | 默认、段落全部移除、重新生成、保存中、保存失败 | 保存 → List；操作 → DraftAdjust |
| `App_DNA_DraftAdjust` | P1 | 弹层/对话框集合 | 段落替换/添加、主题多选、分钟调整、Why this、命名 | CategoryPicker、MultiSelectSheet、DurationDialog、ReasonDialog | 默认、已选、无可添加类别、名称校验 | 确认 / 取消 → Draft |

代码：`features/dna/dna_onboarding_screen.dart`、`dna_flow.dart`、`dna_result_screen.dart`、`routine_draft_screen.dart`。

## 模块三：练习执行与节拍器

- **业务职责**：在同一会话内完成段落计时、单项 Focus、节拍器及结果记录。
- **涉及用户角色**：练习中的本机练习者。
- **端侧归属**：Android 移动端；后台音频与权限属于平台边界。

| 画板/页面资产建议命名 | 优先级 | 页面类型 | 核心内容与结构模块 | 装配组件资产需求 (02_Components) | 状态资产覆盖 (Default/Empty/Loading/Error) | 交互流转去向 |
| --- | --- | --- | --- | --- | --- | --- |
| `App_Session_Overview` | P0 | 开始前确认页 | Before you start、计划段落与目标、切段模式、Start session | SectionSummaryRow、ModeBadge、PrimaryButton | 默认；无段落在启动入口提示，不进入此页 | Practice；返回 List / Editor |
| `App_Practice_Default` | P0 | 高频操作页 | 计划名、段落切换、计时/目标进度、练习选择、练习 BPM、节拍器、底部操作 | SectionStrip、TimerPanel、ExerciseRow、BpmStepper、MetronomePanel、TransportBar | 运行、首尾按钮禁用、目标已到/超时、手动/自动切段 | Focus、Paused、EndConfirm；Next/Previous 更新本页 |
| `App_Practice_Focus` | P0 | Practice 状态画板 | 当前练习独立计时、Focus 目标、Finish Focus | FocusPanel、TimerPanel、PrimaryButton | 进行中、超时、结束 | Rating；退出会话 → EndConfirm |
| `App_Practice_Rating` | P0 | 页内状态画板 | Clean/Shaky/Failed、可选质量与疲劳评分 | OutcomeChip、RatingScale、InlinePrompt | 未评、已评、按设置隐藏评分字段 | 评分更新历史/进阶；继续本页练习 |
| `App_Practice_Paused` | P0 | Practice 状态画板 | 暂停状态、Resume、计时显示 | TimerPanel、TransportBar、StatusLabel | 暂停 / 恢复 | Resume → 原练习状态 |
| `App_Practice_Simple` | P1 | Practice 状态画板 | 段落无练习时的计时模式提示 | TimerPanel、InlineNote、TransportBar | 无练习是合法模式，不是错误或空白页 | Next / Previous；EndConfirm |
| `App_Practice_Insight` | P1 | 页内展开状态 | 当前/目标速度、难度、Clean 连续次数、速度图、标签、参考资料 | StatItem、Sparkline、InfoChip | 有历史、无图数据、字段显示开关 | 返回练习选择或继续 Focus |
| `App_Practice_EndConfirm` | P0 | 对话框 | 结束后不可恢复；保存已练习记录 | ConfirmDialog | 取消 / 确认 | 取消 → 原状态；确认 → Finishing |
| `App_Practice_Finishing` | P0 | 保存状态画板 | 完成中提示，避免重复提交 | InlineProgress、DisabledAction | 保存中、保存失败及重试；失败保留本次会话 | 成功 → Summary；重试 → 保存 |
| `App_Session_Summary` | P0 | 会话总结页 | 实际练习、暂停、段落、尝试次数、Done | SummaryStat、PrimaryButton | 默认；不套用无意义的远程四态 | Done → 顶层入口，History 可查看本次记录 |

节拍器作为 Practice 内组件处理：开/关、独立 BPM、滑块、±及长按、节拍显示、音频失败提示。练习 BPM 与节拍器 BPM 不自动混为同一数值。

代码：`features/practice/practice_launcher.dart`、`session_overview_screen.dart`、`practice_screen.dart`、`session_summary_screen.dart`、`widgets/metronome_panel.dart`、`widgets/exercise_info_panel.dart`。

## 模块四：历史与复盘

- **业务职责**：查看已完成会话并修订记录，让派生进度随评分更新。
- **涉及用户角色**：练习后的本机练习者。
- **端侧归属**：Android 移动端。

| 画板/页面资产建议命名 | 优先级 | 页面类型 | 核心内容与结构模块 | 装配组件资产需求 (02_Components) | 状态资产覆盖 (Default/Empty/Loading/Error) | 交互流转去向 |
| --- | --- | --- | --- | --- | --- | --- |
| `App_History_List` | P0 | 列表页 | 汇总指标、会话卡、底部导航 | SummaryCard、SessionCard、BottomNav、EmptyState | 默认、无记录、加载、读取失败 | Review；底部导航 → Practice / Settings |
| `App_History_Review` | P0 | 复盘编辑页 | 时长、段落、尝试、评分、提速结果、笔记、观察 | ReviewSummary、AttemptTile、OutcomeChip、ProgressionCard、NoteCard | 默认、加载、会话不存在、操作错误；无尝试的段落保留有效记录 | EditNote / EditAttempt / Observation；返回 List |
| `App_History_EditNote` | P1 | 对话框 | 会话笔记或单次尝试描述 | TextDialog、MultilineField | 默认、编辑、保存反馈 | 保存 / 取消 → Review |
| `App_History_Observation` | P1 | 对话框变体 | 新增/编辑观察、删除确认 | TextDialog、ConfirmDialog | 默认、编辑、删除确认 | 保存 / 取消 → Review |

代码：`features/history/history_screen.dart`、`session_review_screen.dart`、`session_review_controller.dart`。

## 模块五：设置与个人练习偏好

- **业务职责**：调整主题、音色、字段和练习行为，维护 DNA 资料。
- **涉及用户角色**：本机练习者。
- **端侧归属**：Android 移动端；系统权限通过说明和外部系统入口表达。

| 画板/页面资产建议命名 | 优先级 | 页面类型 | 核心内容与结构模块 | 装配组件资产需求 (02_Components) | 状态资产覆盖 (Default/Empty/Loading/Error) | 交互流转去向 |
| --- | --- | --- | --- | --- | --- | --- |
| `App_Settings_Main` | P1 | 长滚动设置页 | 主题、练习字段、独立音色、切段模式、后台提示、亮屏、DNA | ThemeOption、SwitchRow、RadioRow、SoundOption、DNAProfileCard、BottomNav | 默认、设置读取/写入错误、DNA 未建档/已建档 | DNA 结果/问卷/草案；系统权限；底部导航 |
| `App_Settings_Fields` | P1 | Main 的滚动定位视图 | 速度/曲线/连胜，难度/标签/视频/技术，疲劳/质量/结果评分开关 | SettingsSection、SwitchRow、InlineNote | 开/关；关闭结果评分时提示不能自动提速 | 返回 Main 相应位置；不新造独立路由 |
| `App_Settings_AudioPractice` | P1 | Main 的滚动定位视图 | 六组音色、手动/自动、后台提示、亮屏 | SoundOption、RadioRow、SwitchRow、PermissionRow | 已选、权限允许/拒绝、请求系统授权 | 外部系统授权 → 返回 Settings |
| `App_Settings_DNAReset` | P1 | 对话框 | 重置答案；保留已存计划与历史 | ConfirmDialog | 取消、确认、失败反馈 | Settings 的 DNA 未建档状态 |

代码：`features/settings/settings_screen.dart`、`settings_controller.dart`、`features/dna/guitar_dna_settings.dart`。

## 主画板计数与优先级口径

11 个 `*Screen` 页面类：Routines List、Routine Editor、DNA Onboarding、DNA Result、Routine Draft、Session Overview、Practice、Session Summary、History、Session Review、Settings。

DNA Onboarding 展开 8 个步骤，所以为 **18 个主画板：17 个 P0 + 1 个 P1 Settings**。其余表格行是弹层族、状态画板或滚动定位视图，不包含在 18 个计数内；同族变体数量在装配阶段确认，避免将集合行误算成一个可交互画板。

全量业务范围建议包含本清单全部 P0/P1。P1 设置也纳入原型闭环；P2/P3 暂无已实现的独立业务模块。本表优先级用于设计排期，不代表源码存在该枚举。

## 核心业务闭环

1. 个性化建计划：Routines → 新建方式 → DNA 八步 → 结果 → 草案调整 → 保存 → Routines。
2. 手动建计划：Routines → 命名 → Editor → 库内选择或自定义 → 位置参数 → 至少一个段落 → 开始前概览。
3. 完成练习：概览 → 段落计时 → 选练习 → Focus → 评分 → 切段 / 继续 → 结束确认 → 保存 → 总结 → History → Review。
4. 修订反馈：Review → 修订尝试结果/笔记 → 派生进度刷新 → 下次练习的信息面板与渐进提速读取更新结果。
5. 偏好闭环：Settings → 主题/音色/字段/模式 → 下次进入练习；DNA → 改答案/再生成/重置。切段模式在会话开始时读取，不假定中途改设置会重写当前会话。

## Figma Page 画布组织架构（待确认）

| Page | 内容 | 写入阶段 |
| --- | --- | --- |
| `00_Flow` | 产品边界、模块树、代码映射、核心闭环、范围说明 | 阶段 1 确认后沉淀架构；Figma 写入须先检查现有文件 |
| `01_Design Tokens` | 已有主题的 Color / Typography / Spacing / Geometry / Motion 规范及证据 | 阶段 2 Token 门禁后 |
| `02_Components` | 基础组件、Variants、业务组合组件；最终家族矩阵和使用位置 | 阶段 2 Components 门禁后；阶段 3 回流；阶段 6 整理 |
| `03_Pilot` | 一张 P0 样板及视觉检查 | 阶段 3 门禁后；定稿后迁入 Screens 并保留 ID，Pilot 留引用指南 |
| `04_Screens` | 五模块主画板、状态、弹层、原型起点；最终功能地图和动线索引 | 阶段 4 创建；阶段 5 原位整理 |

若 Figma 已有内容，先做增量映射再决定 Page 命名；不覆盖未知资产。每张主画板只保留一份物理实例，流程索引引用稳定 node ID。

## 设备画板规范资产（建议，待确认）

| 资产 | 建议规格 | 依据 / 待确认点 |
| --- | --- | --- |
| 平台 | Android 竖屏 | 仓库已实现平台 |
| 业务视口 | 390×844 逻辑像素 | 流水线默认规格；是原型假设，不声称是原截图设备规格 |
| 网格 | 4 列；具体边距、列距以源码主题 Token 提取结果为准 | 只规划网格结构，本阶段不发明 Token 数值 |
| 系统区 | 展示 Android 状态/导航区域；上下 SafeArea 独立容器 | 高度待阶段 2 结合截图比例确认，不套用 iPhone 刘海规格 |
| 长页 | 视口固定、内容纵向滚动；底部操作区按源码固定 | Settings、Editor、Practice 等长内容 |
| 无障碍 | 保留代码触控最小尺寸、选中标记、禁用与错误说明 | 不仅用颜色表达状态；最终数值来自主题定义 |
| 弹层 | BottomSheet / Dialog 使用组件实例；原型采用 Overlay | 状态画板也可使用原型交互组件，具体能力在 Figma 检查后确定 |

## 待确认与风险资产清单

| 项目 | 当前证据 | 拟采用处理 |
| --- | --- | --- |
| 版本 | 已定位本地提交；未比对远端 HEAD | 以本地 `3fc5194` 为本轮基线，远端变更另行增量 |
| 目标文件内容与编辑权限 | 已有链接；门禁前未调用 Figma MCP | 阶段 1 确认后先只读检查；发现冲突保留现有资产 |
| 主主题 | ThemeRegistry 默认 Studio Dark；已有截图 | 主流程 Studio Dark，六主题规范/对照；不展开六倍页面 |
| 语言 | 源码主要英文 | 业务界面英文，架构说明中文；中文本地化属于另一个范围 |
| 状态真实性 | 本地数据流有加载/错误；部分纯展示页没有异步四态 | Default/Empty/Loading/Error 按真实场景标 N/A，不造网络请求 |
| DNA 提交失败 | 流程保存失败可返回 null；具体反馈需进一步核对 | 记录为交互缺口，补反馈前单列确认，不声称源码已有错误页 |
| 目录加载/搜索失败 | 需复核 picker 数据路径的具体提示 | 阶段 4 按实际反馈或经确认的补充处理，不无依据扩画板 |
| 结束练习 | 代码明确不可恢复；保存失败可重试 | 展示结束确认、Finishing 和失败重试，禁止连线到虚构恢复会话 |
| 无练习段落 | 源码定义为合法计时模式 | 单独覆盖 Simple 状态，不显示空数据错误 |
| 系统权限与后台能力 | 有系统权限入口，Figma 无法执行真实音频/系统授权 | 以可点击演示和说明表达边界；应用内状态可交互 |
| 精确时钟、长按和派生图表 | 原型不运行 Dart 状态机 | 展示代表性状态与跳转，计时/音频/数据库行为通过说明对应源码 |

## 本轮确认范围

建议确认：五模块全部 P0/P1；18 个主画板及表中弹层和状态；Android 390×844；英文业务文案；默认 Studio Dark 主流程，保留六主题规范/对照；P0 样板推荐 `App_Practice_Default`，因为它集中计时、选择、节拍器和底部操作组件。

确认回复可用：**确认阶段 1 全部模块**，或逐项修改范围。

门禁依据：用户指定的 `figma-prototype-pipeline/SKILL.md` 要求“每一阶段必须先按模块产出待确认资产明细 → 停止等待用户确认 → 再执行该阶段写入/绘制/整理”；其子技能 `figma-prd-architect/SKILL.md` 明确“人工确认前严格禁止调用 Figma MCP 或生成任何画板图层”。因此此文档是本轮可审阅的具体结果，等待确认后再交接下一阶段。
