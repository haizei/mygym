# Exercio · 阶段 3 样板页模块资产明细（待确认）

流水线进度：阶段 3 / 6 · figma-sample-pilot · 门禁清单

上一阶段已完成：`01_Design Tokens` 与 `02_Components`。六主题201个变量、90个文字样式；44个组件家族，含32个原生矢量图标、39组组件变体与4个单体业务组件。

- 规范看板：https://www.figma.com/design/g6O2q2Al59n0i1yR57gCZn/exercio?node-id=39-37
- 组件总览：https://www.figma.com/design/g6O2q2Al59n0i1yR57gCZn/exercio?node-id=51-592
- 样板 Frame 拟命名：`App_Practice_Default`，位于 `03_Pilot`，本轮只做这一张。
- 本文档是待确认的制作清单；尚未创建样板页或18个业务主画板。

## 产品定位与页面目标

定位：离线吉他练习应用，面向正在持琴练习的人。页面应让人快速确认当前段落、已用时间和下一步动作。

首要视觉焦点是段落计时器；其次是段落名与当前位置；最容易开始的行动是选择练习后点击 Start Focus。Previous / Pause / Next 与 End session 固定在底部。

视觉规格：Android竖屏390×844；Studio Dark模式；正文Manrope，数字Space Grotesk，沿用已确认字体字重近似。页面边距16、段间距16/24、卡片圆角12、控件圆角10、最小触控区域48、主操作高度64。长内容滚动，固定操作区与系统安全区分开。

样例数据：Modern Fusion Practice；5个段落：Warm-up、Picking、Legato、Rhythm、Improvisation；当前段落Warm-up；选中Chromatics，84 BPM、5 min；独立节拍器120 BPM、Off；会话运行中，尚未进入Focus。

## UX审计与证据

本次采用仓库现有Practice截图、源码与`docs/research/reference-apps.md`中已记录的产品研究。后者是仓库2026-07-27的历史研究，本轮不将其当作竞品当前状态的新核验。

| 审计维度 | 证据与判断 | 样板处理 |
| --- | --- | --- |
| 信息层级 | 当前段落与大计时是持琴时最需要远距离读取的信息 | 顶部先段落名，再计时器；数字不混用装饰字族 |
| 进度定位 | 仓库研究强调练习过程中的进度反馈；源码同时呈现段落与会话进度 | 保留段落选择、段落目标、Section 1 of 5与Next提示 |
| 行动路径 | 练习BPM与节拍器BPM属于两个独立状态 | 明确分区，不把84与120误合并 |
| 触达 | 源码48px最小触控规格、64px主操作；底部操作独立于ListView | 小屏内容滚动，常用控制固定，触控区域保留 |
| 状态真实性 | Focus、评分、保存中、保存失败是同一页的不同业务状态 | 样板只展示正常运行；其他状态留给阶段4 |
| 业务边界 | Practice是全屏会话页，源码没有Practice/History/Settings底部导航 | 只放会话控制栏，不插入全局Tab |

## 模块一：安全区、计划名与当前段落 Header

- **布局容器规范**：根容器Vertical Auto Layout，390×844固定视口；顶部Android安全区暂按24px预留，作为原型规格假设，不新增全局Token。滚动内容左右Padding绑定`spacing/md=16`。
- **图层结构树**：`App_Practice_Default` → `[SystemTopInset, ScrollViewport, FixedTransport, SystemBottomInset]`；`ScrollContent` → `PracticeHeader` → `[RoutineName, SectionName]`。
- **文案与排版资产**：计划名「Modern Fusion Practice」绑定`typography/studio-dark/body/sm`；当前段落「Warm-up」绑定`typography/studio-dark/display/lg`。
- **Token绑定**：背景`color/bg/canvas`；主文案`color/text/primary`；计划名`color/text/secondary`；间距`spacing/sm`。
- **拟回流组件**：沿用页面特有标题组；如样板中需要多状态共用，再回流为`Practice/Header`，不抢先扩建组件库。

## 模块二：段落选择 Section Strip

- **布局容器规范**：Horizontal Auto Layout；视口宽358、高至少48；横向内容可滚动；段落间距`spacing/xs=4`。外层裁切仅用于表达横向滚动边界。
- **图层结构树**：`SectionViewport` → `Practice/SectionStrip` Instance → `[Warm-up, Picking, Legato, Rhythm, Improvisation]`。
- **文案与排版资产**：当前「Warm-up」选中；其余显示未选；文字采用`label/md`。
- **Token绑定**：选中描边`color/state/selected`与`stroke/selected=2`；控件背景`color/bg/interactive`；未选描边`color/border/default`；圆角`radius/control=10`。
- **拟回流组件**：在现有`Practice/SectionStrip`原位完善五项与选中覆盖，保留现有ID。只在状态允许时可跳转段落。

## 模块三：段落计时与目标 Timer

- **布局容器规范**：Vertical Auto Layout；宽358、高Hug；内边距按源码计时区水平24、垂直16；段落选择后间距16。
- **图层结构树**：`Practice/Timer` Instance（Kind=Section, State=Running）→ `[Time, Progress, TargetLabel]`。
- **文案与排版资产**：时间「02:18」使用`timer/lg`（76px、Space Grotesk Medium，源600→500已确认）；说明「5 min target」采用`body/sm`。
- **Token绑定**：底色`color/bg/surface`；描边`color/border/default`；卡片圆角12；轨道`color/state/unselected`；段落进度`color/accent`。
- **数据表现**：代表性段落进度约46%；仅展示状态，不承诺Figma运行真实计时器。
- **拟回流组件**：现有Timer与Progress实例；样板调整需回写原组件，保持引用。

## 模块四：会话进度与当前位置 Session Context

- **布局容器规范**：Vertical Auto Layout、Gap4；进度宽358；说明行Horizontal，左右信息分别对齐；与下一模块间距24。
- **图层结构树**：`SessionContext` → `[Practice/Progress Instance, ContextRow]`；`ContextRow` → `[SectionCount, NextSection]`。
- **文案与排版资产**：「Section 1 of 5」「Next: Picking」，采用`body/sm`。
- **Token绑定**：说明`color/text/secondary`；会话进度`color/action/primary`；轨道`color/state/unselected`；厚度6。
- **拟回流组件**：如该组合在Focus等状态中共用，回流`Practice/SessionContext`；不创建不同步的重复页面块。

## 模块五：练习选择、练习BPM与主行动 Exercise Actions

- **布局容器规范**：Vertical Auto Layout、宽358；标题、选择卡、Stepper、主按钮按源码顺序；模块间距8/16。
- **图层结构树**：`ExerciseActions` → `[ExercisesHeading, Item/Exercise Instance, Input/BpmStepper Instance, Button/Action Instance]`。
- **文案与排版资产**：标题「Exercises」用`heading/sm`；练习「Chromatics」、说明「84 BPM · 5 min」；练习BPM「84」用`numeric/lg`；CTA「Start Focus」用`label/lg`。
- **Token绑定**：练习选中背景`color/bg/interactive`、描边`color/state/selected`；主按钮底色`color/action/primary`、文字`color/action/on-primary`；主按钮64px，±触控区48px。
- **交互准备**：Start Focus后续连到阶段4的Focus状态；本阶段不先生成目标画板。
- **拟回流组件**：复用现有Exercise、BpmStepper与Action；如为恢复源码布局而调整内部尺寸，更新主组件，不detach实例。

## 模块六：独立节拍器 Metronome

- **布局容器规范**：Vertical Auto Layout卡片、宽358、Padding16、Gap8；位于同一滚动内容中，允许需要下滚后完整看到。
- **图层结构树**：`Practice/Metronome` Instance（State=Off）→ `[Heading, BpmReadout, TempoControls, ToggleRow]`；`TempoControls` → `[Minus, Slider, Plus]`。
- **文案与排版资产**：「Metronome」「120 BPM」「Off」；大数字沿用源码`numeric/lg`，不重复显示第二个120数值。
- **Token绑定**：卡片底色surface、圆角12；强调accent；轨道unselected；控制触控区域48。
- **业务说明**：节拍器120不跟随练习84自动改动；Focus期间锁定调速的状态留给阶段4。
- **拟回流组件**：沿用已建Metronome，必要的滑块和字体适配回写该主组件。

## 模块七：固定会话操作栏 Transport

- **布局容器规范**：固定在根视口底部；Vertical Auto Layout；横向控制行＋次级结束操作；Android底部安全区暂按24px预留。滚动内容高度扣除该操作区，避免遮住最后内容。
- **图层结构树**：`FixedTransport` → `Practice/Transport` Instance（Paused=False）→ `[Previous, Pause, Next, EndSession]`。
- **文案与排版资产**：「Previous」「Pause」「Next」「End session」；控制图标与标签可读，避免把Previous拆成两行；按钮沿用`label/lg`或源码相应label字阶。
- **Token绑定**：底色`color/bg/surface`；次级按钮interactive；主文案primary、结束操作secondary；±与会话控件最小48、主要控制高度64。
- **状态覆盖**：当前首段Previous保留占位但禁用；Pause可用、Next可用。禁用通过嵌套Button State实现，不通过隐藏按钮伪装。
- **交互准备**：Pause、Next、End session保留交互热点；本阶段不创建其他状态画板和结束确认弹层。
- **拟回流组件**：Transport原位优化，不添加全局BottomNav。

## 拟回流至02_Components的资产汇总

| 资产 | 操作 | 触发条件 |
| --- | --- | --- |
| Practice/Header | 可选ADD | 样板完成后确认跨状态复用价值 |
| Practice/SessionContext | 可选ADD | 段落计数与下一段提示需在多状态统一维护 |
| Practice/SectionStrip | UPDATE原位 | 五段文案、48px触控、横向滚动边界 |
| Practice/Timer、Metronome、Transport | UPDATE原位 | 样板对齐源码后的比例/尺寸调整；不改已确认Token |

## 本阶段验收与门禁

- 仅创建1张390×844样板；长内容可滚动，底部会话控制独立固定。
- 无整页图片；正文为原生Text，模块使用已建实例，结构使用Auto Layout。
- 颜色/间距/圆角绑定Token，文字使用已有Text Style；按确认的字体替代执行。
- 检查当前首段按钮禁用、节拍器独立BPM、Start Focus层级和末尾内容可达性。
- 本轮请确认：**阶段3练习样板全部模块**。确认后再创建`App_Practice_Default`。

门禁依据：用户指定的`figma-sample-pilot/SKILL.md`要求“输出上述按模块垂直细分的图层/文案/Token资产表后，必须停止等待用户确认。未确认前禁止在Figma创建画板”。


## 执行交接结果

已获用户确认并执行。样板 `App_Practice_Default`：`75:3`，页面 `03_Pilot`：`75:2`。新增 Practice/Header、Practice/SessionContext、Practice/TransportButton 已回流 02_Components。最终检查：390×844、35 个有效组件实例、无图片/字体错配/意外溢出；滚动 281px；固定操作栏未遮挡内容；Previous 禁用；练习84 BPM、节拍器120 BPM。原型连线由阶段4统一完成。验证记录见 exercio-figma-pilot-validation.json。
