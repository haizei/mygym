# openGym Screens 整理记录

目标：[Figma 阅读指南](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=181-1750)

## 模块目录

|模块|用户目标|画板数|
|---|---|---|
|01 启动与首次使用|进入产品，了解从哪里建立计划|2|
|02 首页与训练入口|查看今日计划，开始或恢复训练|4|
|03 计划与动作管理|从日程进入方案，创建计划并配置动作|9|
|04 训练执行与完成|选择训练、记录组次、休息并结束训练|6|
|05 训练统计|理解训练表现，进入历史记录|2|
|06 训练历史与详情|回顾记录，并从历史再次开始训练|3|

## 关键动线

### 启动与首次使用

入口：ENT-01 点击继续 → S01；S09 等待 1.2 秒 → S01（首页模块）。两者是独立演示起点，并非先后步骤。

### 首页与训练入口

HOM-01 开始今日训练 → OVR-04；选择训练 → WKT-01。S01 建立计划 → OVR-01 / PLN-02；S02 恢复 → WKT-02；S10 重试 → HOM-01。四张是同一首页的不同状态。

### 计划与动作管理

主线：PLN-01 → PLN-02 → PLN-03 → OVR-02 ⇢ OVR-03 → PLN-03 → PLN-02。下排对应入门分支与上方主页面状态。OVR-01 来源：S01 / PLN-02 / S03；OVR-02 来源：PLN-03 / S04 / WKT-01 / WKT-02。OVR-03 确认固定返回 PLN-03，训练来源的上下文待核对。

### 训练执行与完成

主线：WKT-01 → OVR-04 → WKT-02 → OVR-05 ⇢ OVR-06 → HOM-01 / HIS-01。S08 跳过休息 → WKT-02；收起训练 → S02（首页）。称重也由 HOM-01 / S10 / HIS-02 调用；取消按钮固定去 WKT-01，关闭热区与遮罩则 CLOSE。完成汇总由 SWAP 打开。

### 训练统计

STA-01 查看全部 / 标题历史入口 → HIS-01；点击训练记录 → HIS-02（Overlay）。S06 去训练 → WKT-01。统计空态与默认页并列阅读。

### 训练历史与详情

HIS-01 点击记录 → HIS-02（Overlay）；HIS-02 CLOSE 回到调用上下文，其他按钮可到 OVR-04 / WKT-02 / HIS-01。详情也由 STA-01 调用；固定跳历史按钮不同于 CLOSE。S07 开始训练 → WKT-01。

## 名称映射与资产清单

所有画板保留原 node ID，尺寸均为 390 × 844。来源按现有原型连线记录；全局 Tab 的调用也包含在下表。

|旧名称|新名称|node ID|实际调用来源|
|---|---|---|---|
|OG/ENT-01|ENT-01｜启动与首次使用｜首次使用｜Default|[99:2](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=99-2)|独立演示起点|
|OG/S09|S09｜启动与首次使用｜启动加载｜Loading|[100:1807](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1807)|独立演示起点|
|OG/HOM-01|HOM-01｜首页与训练入口｜今日计划｜Default|[99:30](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=99-30)|PLN-01、PLN-02、WKT-01、STA-01、HIS-01、OVR-05、OVR-06、S01、S02、S03、S06、S07、S10|
|OG/S01|S01｜首页与训练入口｜首页空计划｜Empty|[100:1249](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1249)|ENT-01、S09|
|OG/S02|S02｜首页与训练入口｜首页训练进行中｜In-progress|[100:1319](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1319)|WKT-02、S08|
|OG/S10|S10｜首页与训练入口｜首页连接失败｜Error|[100:1828](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1828)|独立演示起点|
|OG/PLN-01|PLN-01｜计划与动作管理｜计划日程｜Default|[99:159](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=99-159)|HOM-01、PLN-02、WKT-01、STA-01、HIS-01、S01、S02、S03、S06、S07、S10|
|OG/PLN-02|PLN-02｜计划与动作管理｜方案列表｜Default|[99:344](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=99-344)|PLN-01、PLN-03、OVR-01、S01、S03、S04|
|OG/PLN-03|PLN-03｜计划与动作管理｜方案编辑｜Default|[99:442](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=99-442)|PLN-01、PLN-02、OVR-03|
|OG/OVR-02|OVR-02｜计划与动作管理｜动作选择弹层｜Default|[100:1027](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1027)|PLN-03、WKT-01、WKT-02、S04、S05|
|OG/OVR-03|OVR-03｜计划与动作管理｜动作配置弹层｜Default|[100:1118](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1118)|PLN-03、OVR-02|
|OG/OVR-01|OVR-01｜计划与动作管理｜入门方案弹层｜Default|[100:1001](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1001)|PLN-02、S01、S03|
|OG/S03|S03｜计划与动作管理｜方案列表空态｜Empty|[100:1425](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1425)|独立演示起点|
|OG/S04|S04｜计划与动作管理｜方案编辑无动作｜Empty|[100:1494](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1494)|PLN-02、S03|
|OG/S05|S05｜计划与动作管理｜动作搜索无结果｜Empty|[100:1527](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1527)|OVR-02|
|OG/WKT-01|WKT-01｜训练执行与完成｜选择训练｜Default|[99:554](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=99-554)|HOM-01、PLN-01、PLN-02、STA-01、HIS-01、OVR-04、S01、S02、S03、S06、S07、S10|
|OG/OVR-04|OVR-04｜训练执行与完成｜训练前称重弹层｜Default|[100:1160](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1160)|HOM-01、WKT-01、HIS-02、S10|
|OG/WKT-02|WKT-02｜训练执行与完成｜训练记录｜In-progress|[99:655](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=99-655)|HIS-02、OVR-04、S02、S08|
|OG/OVR-05|OVR-05｜训练执行与完成｜结束菜单弹层｜Default|[100:1184](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1184)|WKT-02、S08|
|OG/OVR-06|OVR-06｜训练执行与完成｜完成汇总弹层｜Success|[100:1208](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1208)|OVR-05|
|OG/S08|S08｜训练执行与完成｜训练休息中｜In-progress|[100:1687](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1687)|WKT-02|
|OG/STA-01|STA-01｜训练统计｜统计概览｜Default|[99:896](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=99-896)|HOM-01、PLN-01、PLN-02、WKT-01、HIS-01、S01、S02、S03、S06、S07、S10|
|OG/S06|S06｜训练统计｜统计无记录｜Empty|[100:1557](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1557)|独立演示起点|
|OG/HIS-01|HIS-01｜训练历史与详情｜训练历史｜Default|[99:1001](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=99-1001)|STA-01、HIS-02、OVR-06|
|OG/HIS-02|HIS-02｜训练历史与详情｜训练详情弹层｜Default|[99:1158](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=99-1158)|STA-01、HIS-01|
|OG/S07|S07｜训练历史与详情｜训练历史空态｜Empty|[100:1619](https://www.figma.com/design/lvFX678GbPoE2IZPjYEybP/opengym?node-id=100-1619)|独立演示起点|

## 保护与验收

- 原画板 26 张，原 node ID、390 × 844 尺寸、内部内容与内部坐标、组件实例关联、变量与样式关联保持。
- 444 个实例；145 个原型交互逐节点核对无差异；7 个演示起点保持。
- 6 个模块 Section；所有原画板在分区边界内；原外部标签一起移动。
- 阅读指南与计划模块截图检查通过，无重叠与裁切。
- 原始五块 P0 规划说明移动至左侧参考归档区，内容保留。

## 保留原行为的问题与缺口

- OVR-03 确认固定去 PLN-03，来自训练场景时可能丢失上下文。
- OVR-04 取消固定去 WKT-01，区别于遮罩和关闭按钮 CLOSE。
- HIS-02 从统计和历史均可打开，固定跳历史按钮与 CLOSE 的返回语义不同。
- 动作库独立业务页未建立；其余缺失业务页和状态不在整理时补建。
- 输入、计时、数据为原有静态演示，整理不扩展业务行为。

## 恢复数据

同目录 `screen-organization-before.json` 保存整理前的节点位置、名称、父级（原画板均直属 29:5）、原型连线和起点。`screen-organization-plan.json` 保存完整名称映射与模块安排。`screen-organization-result.json` 保存本次新建节点、修改节点和验收信息。

