---
name: figma-component-organizer
description: "Organize an existing Figma component library into browsable families with variant matrices, usage guidance, dependency maps, and links to consuming screens. Use when a Components page is difficult to navigate or needs structured handoff documentation."
---

# Figma Component Organizer

让读者能够回答：组件做什么、什么时候用、有哪些属性与状态、依赖什么、哪些页面正在使用。

默认整理已有组件、变体展示和使用说明。修改组件 API、合并组件或补建新组件，应单独列为变更，不混入排版整理。

## 1. 读取组件与使用证据

使用当前环境可用的 Figma 工具；执行 Plugin API 前加载 figma-use。涉及组件属性或变体变更时加载 figma-generate-library。

读取目标 Components Page，记录：
- Component Set、独立 Component、Variant 的 ID、名称和父级；
- 属性的确切 key、类型、默认值和可选值；
- Auto Layout、尺寸、Variables、Text Styles、Effect Styles 绑定；
- 嵌套 Instance 对应的主组件；
- 已有 Section、说明、示例与排布。

读取用户指定的 Screens，按 mainComponent ID 追踪使用位置。将变体归到所属家族，同时保留使用的具体变体；区分页面直接使用与通过嵌套组件间接使用。

只有实际扫描过的页面才能标为已核对。无法扫描的页面记为「未核对」，不能把没有查到引用等同于没有使用。

依赖关系分为：组件直接依赖、传递依赖、Token/Style 依赖。只有顺着 alias 解析后的值才标为解析值；绑定证据缺失时明确说明。

## 2. 分类与统计

先按用途分组，再在组内按组件家族组织。推荐分类可根据实际库调整：
- 图标与视觉基础；
- 输入与操作控件；
- 导航与系统框架；
- 通用内容容器；
- 具体业务模块；
- 数据展示；
- 状态与反馈。

每个组件家族只有一个主归属，跨模块使用通过索引引用，不复制另一份主组件。

分开报告：
1. 家族数：一个 Component Set 计一个家族，独立 Component 单列；
2. Variant 数：集合下的具体变体；
3. 独立图标数；
4. 已扫描页面中的 Instance 数。

不把 Variant 当作不同业务组件，也不把图标数量当作业务组件数量。

输出目录：

| 分区 | 组件家族 | 主节点 ID | 用途 | 现有变体/属性 | 直接依赖 | 使用页面 | 证据/缺口 |
|---|---|---|---|---|---|---|---|

## 3. 为每个家族建立阅读卡

卡片或说明板包含：
- 中文名称和原始组件路径；
- 一句话用途与适用场景；
- 使用已有 Instance 创建的常见示例；
- 现有属性表：属性名、类型、默认值、使用方式；
- 状态或变体矩阵；
- 直接依赖和使用页面引用。

用途基于名称或外观推断时标为推断；有代码、现有文档或页面实例支持时注明来源。

简洁控件使用紧凑矩阵；复杂业务组件使用真实内容展示。
不要给 24px 图标或几十像素高的控件分配统一的巨大网格。

## 4. 变体矩阵

根据现有属性选择有意义的轴：
- Button：行按 appearance，列按 state；size 分子组；
- Text Field：empty / filled / focus；
- Tab Bar：active item；
- 业务卡片：业务模式或任务状态。

矩阵只展示真实存在的组合。缺失组合标为「未建立」或按证据标为「不适用」，不自动补齐所有属性的笛卡尔积。

保留 Variant 名称中的 key=value 语义。整理时不将属性 key 重命名、不删除属性、不重建 Component Set。

若现有属性命名混合了不同概念，例如 state 同时表示 layout、activeTab 或 mode，登记为 API 改进建议；本轮不直接修改。

## 5. 展示组成关系与使用位置

对业务组件给出精简装配链，例如：

Exercise Card
  → Set Row
    → Stepper
    → Check
  → Button

不要把所有传递图标依赖全部画成连线。概览呈现关键装配层级，完整依赖留在资产表。

展示页引用使用 Component Instance；主组件只保留原有一份。
为重点组件给出一到两个已扫描页面示例，用画板 ID 和 node 链接定位。
展示副本明确标注为示例，不成为第二份组件或页面事实来源。

## 6. 画布组织

在顶部建立组件库阅读指南：
- 分类目录及各组用途；
- 推荐查找顺序；
- 家族、Variant、Instance 的区别；
- 常用组件及其页面入口；
- 已知缺口与扫描范围。

按实际用途创建 Figma Section，将现有集合或独立主组件原位归类。
完整移动 Component Set；不要把其中的 Variant 拆到不同 Section。
组件名称保留原始英文路径，中文名称放在分区与说明中。

示例、属性表和状态矩阵紧邻所属主组件。采用实际内容宽高计算间距，给较大的弹层与业务卡片独立空间。

## 7. 执行边界与恢复

用户只请求方案时，输出分类与示例，不修改 Figma。
用户已明确授权执行整理时，完成范围内的排布、分区、目录和说明，不重复请求同一范围的许可。

执行前保存节点 ID、名称、父级、位置、属性定义和已扫描引用，形成可恢复记录。
保留主组件 ID、组件 key、Variants、属性 key、实例引用、布局及规范绑定。
外部维护的组件注册表也应记录；若调整会影响按名称或路径查询的调用，优先保留原始名称。

相似组件先列出差异、用途与引用，作为合并候选；不擅自删除或合并。
因工具限制无法安全改变父级时，保留父级，通过标题与背景分区完成整理。

组件的 purpose 和 usage 可写入 Component/Component Set 的 description；普通 Frame 的说明使用可编辑文本，不写不支持的 description 属性。

## 8. 验收与交付

核对：
- 既有主组件、集合及变体的 ID 和数量保持一致；
- 属性定义、组件 key 与已有实例关联保留；
- Variables/Styles 绑定不因移动丢失；
- 每个家族有明确用途、变体与可用属性说明；
- 矩阵名称与真实属性一致，没有展示不存在的状态；
- 示例不裁切、不重叠，图标和文字在正常缩放下可辨认；
- 使用位置标明扫描范围，未发现引用的组件不被自动删除。

返回整理后的 Figma 链接、分类目录、关键装配关系、资产映射和未处理的改进建议。
