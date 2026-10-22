# openGym — 页面结构与组件装配清单

日期：2026-10-20。阶段：用户已确认，组件、26 个画板及原型交互已写入 Figma。
结果：95 个 Master Components（含 32 个代码 SVG 图标）、13 个变体集合、444 个页面内实例、145 条原型交互、7 个演示起点。
界面入口：https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=99-30
原型入口：https://www.figma.com/proto/lvFX678GbPoE2IZPjYEybP/opengym?page-id=29%3A5&node-id=99-2&starting-point-node-id=99%3A2
资产 ID、连线及验证见 figma-flow-validation.json。已检查关键画板截图、实例关系和跳转目标；图表圆点越界与统计页历史入口已修正。
原型展示静态示例数据，数字输入、计时、同步重试及保存不连接运行时引擎。连接服务器、动作库独立路由、AI、签到、照片、管理等入口不在本批完整流程内；本批动作选择使用弹层。
目标文件：lvFX678GbPoE2IZPjYEybP / opengym。
范围：首次本地使用、建计划、启动训练、训练记录、完成及历史回看。

## 1. 开始实施时的基线

- opengym/theme：36 个颜色变量，light/dark 两种 mode。
- opengym/foundation：39 个数值变量；17 个 Text Styles，4 个 Effect Styles。
- 02_Components（29:3）：4 块说明板，0 个 Master Components、0 个 Instances。
- 04_Screens（29:5）：5 块说明板，0 个实际 App 画板。
- 保留这些说明板与既有资产 ID；新增组件和页面放在现有内容右侧。

## 2. 首批页面（10 个常规画板）

统一移动端 390×844，简体中文、默认 lime、light 主题。Dark 由现有变量支持；本批不复制整套深色页面。

| 编号 | 页面 | 顶部 / 正文 / 底部结构 | 主要装配实例 |
|---|---|---|---|
| ENT-01 | 首次使用 | 产品标识、介绍 / 本地或服务器选择 / 说明 | button、card |
| HOM-01 | 首页 | 日期与设置 / 周日历、今日计划、体重摘要 / tabbar | header、week-strip、today-card、metric-card、tabbar |
| PLN-01 | 计划·日程 | 标题与选项 / 固定周、星期分配 / tabbar | header、segmented、grouped-row、chip、tabbar |
| PLN-02 | 计划·方案 | 标题 / 方案列表与新建 / tabbar | header、segmented、routine-row、button、tabbar |
| PLN-03 | 方案编辑 | 返回、名称 / 动作列表、配置 / 添加动作操作 | header、text-field、exercise-row、button |
| WKT-01 | 选择训练 | 标题 / 今日计划、其他方案 / 自由训练 | header、card、routine-row、button、tabbar |
| WKT-02 | 训练中 | 收起、训练名、结束 / 动作卡与逐组记录 / 休息栏 | header、exercise-card、set-row、stepper、check、rest-bar |
| STA-01 | 统计 | 标题、历史入口 / 摘要、趋势、近期训练 / tabbar | header、metric-card、chart-panel、workout-row、tabbar |
| HIS-01 | 训练历史 | 返回、标题、补录入口 / 按日期训练列表 / 导航 | header、workout-row、button、tabbar |
| HIS-02 | 训练详情 | 返回、训练名 / 日期、时长、动作与组记录 / 重复/编辑入口 | header、metric-card、exercise-row、button |

HIS-02 在源码中是 WorkoutDetail 弹层：原型采用可滚动大弹层框架，而非新增应用路由。表中单列为可审阅的详情画板。

## 3. 关键弹层（6 个）

| 编号 | 弹层 | 内容与出口 |
|---|---|---|
| OVR-01 | 入门方案选择 | 方案简介与载入；完成进入计划 |
| OVR-02 | 动作选择器 | 搜索、筛选、动作列表；选中进入配置 |
| OVR-03 | 动作配置 | 组数、次数、重量、休息及渐进策略；保存回编辑 |
| OVR-04 | 训练前称重 | 数值、单位、开始训练；可跳过称重 |
| OVR-05 | 结束训练菜单 | 保存、取消；提前结束说明及放弃分支 |
| OVR-06 | 训练完成汇总 | 时长、容量、组数、PR、下一步；返回首页/查看历史 |

## 4. 补充状态（10 个）

1. 首页：无计划空态，引导载入方案或创建。
2. 首页：训练进行中，主操作为恢复。
3. 方案列表：空态，引导新建或载入入门计划。
4. 方案编辑：无动作，引导添加。
5. 动作选择器：搜索无结果，提供清除搜索或自定义入口说明。
6. 统计：首次训练前空态，引导开始训练。
7. 历史：暂无记录，引导开始或补录。
8. 训练：休息倒计时状态，提供跳过/调整入口。
9. 启动：数据初始化 Loading，使用代码中的启动标识；本地列表不虚构网络骨架加载。
10. 首页：连接失败 Error，显示同步状态条与处理入口，同时说明本机修改保留。

四态考虑：所有页面具 Default；Empty 按本地数据条件；Loading 用于启动/异步连接；Error 用于连接失败或表单校验。纯本地编辑不添加不存在的服务端提交流程。表单非法值用组件校验反馈呈现，不增加重复整页。

本批合计：10 个常规页面画板 + 6 个关键弹层 + 10 个补充状态 = 26 个画板。

## 5. 待补 Master Components 与变体

在 02_Components 按依赖顺序建立；所有主容器用 Auto Layout，颜色/间距/圆角绑定既有变量，文字引用既有样式。只对本批用到的变体建实例，避免无用途的笛卡尔积。

| 家族 | 本批变体/属性 | Tokens/Styles |
|---|---|---|
| button | primary/plain/tinted/danger；default/sm；default/pressed/disabled 按使用建立；label/icon 属性 | brand/*、radius/control、typography/button/* |
| icon-button | default/pressed/disabled；图标属性 | surface/*、radius/pill |
| header | main/back/workout；title/subtitle/action 可选 | typography/title/mobile、body/subtitle |
| tabbar | home/plan/start/stats/library；start/resume | brand/*、text/*、typography/tab/label |
| card / metric-card | 内容 slot；标题、数值、单位 | surface/default、radius/card、spacing/card、metric/large |
| grouped-row | normal/danger；icon/subtitle/value/accessory 可选 | border/hairline、spacing/row-gap、body/row、caption/row |
| segmented | schedule/routines；选中/未选中 | component/segmented/track、effect/segmented/selection |
| text-field / search-field | empty/filled/focus；文本/单位/clear 属性 | surface/*、text/*、radius/control、body/default |
| stepper / set-row | 默认/禁用/非法；力量组重量×次数；未完成/完成 | surface/secondary、status/danger、opacity/*、body/row |
| check | off/on；默认/按下 | brand/*、text/quaternary |
| chip / tag | 默认/选中，普通/强调 | brand/soft、radius/pill、radius/6、chip/default、tag/default |
| routine-row / exercise-row | 名称、摘要、图标/媒体 slot；可点击 | grouped-row、tag、icon-button |
| week-strip / today-card | 周日选择、已完成/今日/休息；开始/恢复 | card、chip、button |
| exercise-card | 动作标题、组记录、增加组、动作选项 | set-row、stepper、check、button、card |
| rest-bar | 运行/结束；计时、跳过、调整 | brand/*、surface/*、button |
| workout-row / chart-panel | 日期、训练名、训练摘要；趋势示例 | card、body/list、caption/default |
| state-panel / sync-banner | empty/loading/error；文案、操作与状态图标 | text/*、status/*、button |
| sheet / center-dialog | 标题、关闭、内容 slot、主要操作 | component/sheet/background、radius/sheet、effect/dialog/center |

图标复用代码的 SVG path，不用旋转线条手拼。动作媒体使用代码对应的可用资源；找不到资源则明确留作媒体槽，不用装饰图片替代真实动作。

## 6. 原型闭环

1. 首次使用 → 本地模式 → 首页空态 → 入门方案 → 计划方案列表。
2. 方案列表 → 方案编辑 → 动作选择 → 配置保存 → 日程 → 首页今日训练。
3. 首页开始 / 训练选择 → 称重或跳过 → 训练记录 → 休息 → 下一组 → 结束菜单 → 完成汇总。
4. 完成汇总 → 首页 / 统计 → 历史 → 详情 → 重复今日 → 开始训练。
5. 训练页收起 → 首页进行中 → 恢复训练；结束菜单取消返回训练。
6. 搜索无结果 → 清除搜索；连接失败 → 处理入口说明；每个空态有可点击下一步。

Figma 使用 Navigate/Open overlay/Back/Close overlay；不把演示数据或静态数字变化声称为真实数据存储和计时引擎。服务器配对、AI、签到、照片、管理员等流程沿用既有架构，另批展开。

## 7. 交付验收

- Master Components 在 02_Components，屏幕通过 Instances 装配，保持组件关联。
- 本批 Frame 使用 390×844，长内容可滚动；训练中隐藏 tabbar，符合代码。
- 三段结构按页面实际任务适用，不向每个查看页强加固定提交按钮；34px 为所选原型设备的底部展示安全区，不覆盖代码运行时 inset。
- 变量、样式、组件引用返回 ID 并落盘；保留现有说明板。
- 关键点击形成开始、取消、返回、完成、历史回看闭环。
- 检查文本截断、容器溢出、组件关联与原型目标；修正后截图交付。

执行范围已完成：先建立本清单所需 Master Components，再通过实例装配 26 个画板并配置基本原型交互。部分实例内部的交互通过透明原生热区实现，组件关联保留。此批同时完成 tokens-system 的阶段 B 组件预案。
