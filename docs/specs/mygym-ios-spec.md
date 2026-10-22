# Spec: MyGym 原生 iOS 重写与商业化落地

## Problem Statement

现代力量训练爱好者在记录训练、推导负荷与管理身体疲劳时面临严重的工具割裂：
1. **商业订阅软件体验僵化**：市面主流健身 App 大多采用高昂的月度订阅制，数据被云端绑架；其所谓的“进阶建议”往往是粗糙的人工经验数字，缺乏可解释、严谨的运动生理学数学模型。
2. **Web / 跨平台应用质感匮乏**：基于网页或套壳封装的应用在健身房地下室弱网或无网络环境下极易出现白屏、卡顿甚至丢失训练打勾数据；无法深度利用 iOS 原生系统特性（如锁屏与灵动岛实时组间休息倒计时、CoreHaptics 机械感触感震动反馈、Apple HealthKit 双向体征联动、桌面小组件极速展现）。
3. **极客开源工具门槛过高**：原版优秀的自托管开源系统（如 openGym）依赖用户自行搭建 NAS / Docker 容器，并采用网页通行密钥（Passkey），对普通受训者门槛过高；且其采用的单文件 JSON 存储机制在商业 iOS 系统中面临桌面小组件 30MB 严格内存配额被杀进程、多设备覆盖性同步冲突等致命瓶颈。

受训者需要一款既拥有极致纯粹原生 iOS 丝滑质感、低门槛多渠道账号登录、零延迟离线记录，又完整继承经过科学检验的纯函数渐进超负荷与 ACWR 疲劳模型的现代化力量训练应用。

---

## Solution

将 openGym 的运动科学内核与现代 iOS 原生体系深度融合，打造面向 App Store 商业化发布的 **MyGym 原生 iOS 应用**：
1. **科学大脑与无状态推导**：将线性进阶、Greyskull LP、双重进阶、三重进阶与 Epley 减载算法，以及 ACWR 肌肉疲劳半衰期衰减模型，封装为独立的纯 Swift 领域引擎，由历史数据实时动态推导下一次目标重量、组数与次数，绝不依赖易漂移的中间状态计数器。
2. **Local-First 本地优先持久化**：采用 iOS 17 SwiftData 构建可迁移的实体数据层，保证健身房无网离线状态下 0 毫秒极速记录，同时为灵动岛与桌面小组件提供极低内存占用的定向快速查询能力。
3. **原生天花板级用户体验**：
   - 组间休息自动同步至锁屏与灵动岛（Live Activities / ActivityKit），倒计时结束伴随细腻触感震动与提示音；
   - 18 块主要肌群基于解剖学坐标以纯 SwiftUI 矢量 Path 渲染，支持 Metal 硬件加速、弹性触控动效与疲劳色阶平滑渐变；
   - 与 Apple HealthKit 深度双向联动，静默读取体征并写入力量训练卡路里。
4. **商业化闭环与全功能保留**：
   - 接入适合国内与国际市场的四合一极简账号（手机号验证码、微信授权、支付宝授权、Sign in with Apple）；
   - 自建后台提供增量变更同步（Change-based 3-Way Merge），彻底解耦单机文件覆盖冲突；
   - 保留并原生化 AI Coach，通过结构化契约提供人机审批闭环的智能周计划调整与练后疲劳复盘。

---

## User Stories

### 账号与认证 (Authentication & Profile)
1. As a new lifter, I want to sign in using Sign in with Apple, so that I can authenticate with Face ID in one tap without revealing my personal email.
2. As a domestic mobile user, I want to log in using WeChat or Alipay authorization, so that I can quickly onboard using my preferred daily super-app account.
3. As a mobile phone user, I want to register and log in using an SMS verification code, so that I have a reliable fallback login method independent of third-party platforms.
4. As a multi-device lifter, I want my account identity to unify across Apple, WeChat, and phone credentials, so that I can access the same training profile on both my iPhone and future devices.
5. As a privacy-conscious athlete, I want all my sensitive health and workout history to be encrypted on-device and tied to my user token, so that unauthorized parties cannot inspect my fitness data.

### 训练前规划与课表编排 (Routines & Planning)
6. As a lifter planning my split, I want to create structured routines (such as Push, Pull, Legs, or Upper/Lower), so that my weekly training distribution is clear and actionable.
7. As a structured trainee, I want to reorder exercises within a routine using native drag-and-drop gestures, so that I can adapt the exercise execution sequence to gym equipment availability.
8. As a strength athlete, I want to assign individual progression policies to each exercise (Linear, Greyskull LP, Double, Triple, or Off), so that compound lifts and isolation exercises advance according to their optimal adaptation curves.
9. As a lifter scheduling a recovery period, I want to toggle a routine as a Deload Routine, so that workouts logged during this week do not pollute my baseline progression thresholds or trigger accidental deloads.
10. As a trainee with specific gym gear, I want to configure my personal dumbbell inventory ladder, so that weight increase suggestions always snap to dumbbells my gym actually possesses.
11. As a barbell lifter, I want to configure exercise-specific load increments and bar weight floors, so that micro-plates or standard 2.5 kg jumps are respected deterministically.

### 动作库与媒体浏览 (Exercise Catalogue)
12. As a lifter exploring movement variations, I want to browse an offline-first library of over 1,300 exercises, so that I can find movements matching my equipment and muscle focus.
13. As an anatomy-guided trainee, I want to filter the exercise catalogue by tapping target muscle groups or selecting available equipment, so that I can locate suitable substitutes in seconds.
14. As an athlete checking form, I want to view smoothly cached animated movement demonstrations without network lag, so that I can verify execution technique at the rack.
15. As a trainee with custom needs, I want to create my own user-defined exercises with specific muscle assignments, so that niche gym machines or rehab variations are tracked accurately.

### 核心训练执行流 (Active Workout Session)
16. As an active lifter, I want to start today's scheduled workout with pre-filled weights and reps derived from my last session's performance, so that I don't have to recall what I lifted previously.
17. As an athlete at the bar, I want to see an unambiguous Progression guidance line above each exercise card explaining why this weight was chosen, so that I trust the prescribed loading.
18. As a lifter completing a set, I want to tap a checkbox to mark the set done and feel an immediate crisp haptic vibration, so that I receive tactile confirmation while catching my breath.
19. As a progressive trainee, I want to log my actual completed reps and weights if they deviate from prescription, so that the engine judges my real performance rather than a theoretical plan.
20. As an RIR-aware lifter, I want to optionally log Reps in Reserve (RIR) or RPE on heavy sets, so that my fatigue score accurately reflects proximity to muscular failure.
21. As an athlete doing warm-ups, I want auto-generated ramped warm-up sets preceding my work sets, so that my central nervous system is primed without polluting work set volume.
22. As an advanced lifter, I want to flag drop sets and rest-pause clusters within a set row, so that intensification techniques are tracked with appropriate fatigue discounts.
23. As a busy trainee, I want to execute supersets with alternating row prompts, so that I can pair antagonist muscle groups without losing my place in the session.
24. As an athlete in a basement gym, I want my entire workout session state to persist locally in real-time, so that loss of cellular reception never freezes the app or loses checked sets.

### 组间休息与锁屏实时活动 (Rest Timer & Live Activities)
25. As a lifter finishing a heavy work set, I want the rest timer to start automatically, so that my recovery interval between sets is consistently controlled.
26. As an athlete resting between sets, I want the countdown timer to appear in the Dynamic Island and on the Lock Screen via ActivityKit, so that I can monitor remaining rest time without unlocking my phone.
27. As a resting trainee, I want a gentle haptic warning three seconds before rest ends, so that I can mentally prepare to step under the bar.
28. As a focused lifter, I want an audio chime when the rest timer reaches zero, so that I know exactly when my rest window has concluded even if my phone is face-down on the bench.
29. As an athlete pacing a workout, I want to easily add or subtract 30 seconds from the active rest timer from the Dynamic Island, so that I can accommodate unexpected fatigue.

### 自动进阶与减载机制 (Progression Engine)
30. As a linear progression lifter, I want the engine to add one weight increment when all target reps across all sets are achieved, so that I continuously build absolute strength.
31. As a Greyskull LP lifter, I want the final AMRAP set to earn a double weight jump if I exceed the target rep count by two-fold, so that rapid strength gains are rewarded.
32. As a Greyskull LP lifter, I want a single missed set to instantly trigger a 10% load reset, so that my fatigue clears and form does not break down.
33. As an isolation trainee using double progression, I want the weight to hold steady while my reps climb from the bottom to the top of my rep range, so that I build volume before jumping load.
34. As a double progression lifter, I want the weight to advance only when all work sets hit the top of the rep range, resetting target reps to the bottom of the range, so that transitions are sustainable.
35. As an athlete using triple progression, I want the engine to advance reps first, then append additional sets up to my configured ceiling, and finally advance weight, so that training volume builds systematically.
36. As a lifter hitting a plateau, I want the app to detect consecutive misses at the same load and prescribe an Epley-based 90% deload target, so that I can restart accumulation safely.
37. As a lifter who made slight progress during a stall, I want rep improvements over prior sessions at the same weight to prevent the stall counter from incrementing, so that genuine progress is not penalized.

### 疲劳监控与人体热力图 (ACWR Recovery & BodyMap)
38. As an athlete planning my split, I want to view an anatomical vector BodyMap displaying per-muscle fatigue, so that I can visually verify whether targeted muscles have recovered.
39. As a visual user, I want the BodyMap to render as native GPU-accelerated vector shapes, so that muscle contours remain razor-sharp on Retina screens and Dynamic Type scaling.
40. As an interactive user, I want to tap any muscle on the BodyMap to feel a subtle spring animation and haptic feedback, so that inspecting muscle state feels tactile and responsive.
41. As a trainee examining a sore muscle, I want tapping a muscle to reveal its current fatigue status, estimated retained strength, and last-trained exercises, so that I make informed training decisions.
42. As a lifter returning from a break, I want the ACWR model to account for low chronic exposure and reflect heightened acute fatigue, so that I am warned against excessive sudden volume.
43. As a recovering athlete, I want muscle fatigue to decay according to muscle-specific exponential half-lives (24h for arms, 30h for torso, 48h for legs), so that recovery timers match real physiology.
44. As a lifter monitoring readiness, I want fatigue colors to blend smoothly from deep red to yellow to green, so that recovery states feel continuous rather than like abrupt discrete bucket jumps.

### 智能教练与周期复盘 (AI Coach & Human-in-the-Loop)
45. As a trainee desiring periodic advice, I want to request an AI Coach review of my weekly training volume, so that I receive structured feedback on muscular imbalances or stalling lifts.
46. As a lifter protecting my routine, I want all AI Coach recommendations presented as an explicit draft card with individual change toggles, so that the AI cannot mutate my plan without my approval.
47. As an athlete finishing a demanding workout, I want to submit a post-workout debrief with my perceived effort, so that the AI Coach can provide contextual insights on my recovery trends.
48. As an injured lifter, I want to prompt the AI Coach with temporary pain constraints, so that it drafts appropriate exercise substitutions without abandoning my overall split.

### 健康生态与云端同步 (Apple Health & Cloud Sync)
49. As an Apple Health user, I want MyGym to read my morning body weight and resting heart rate, so that my body metric trends are tracked without manual double-entry.
50. As an Apple Watch wearer, I want completed workouts to write active calories and duration to Apple Health as Traditional Strength Training, so that my fitness rings close seamlessly.
51. As an active mobile subscriber, I want my workout history, routines, and metric logs to sync incrementally in the background to the cloud, so that switching phones preserves my training continuity.
52. As a trainee in an airplane or offline gym, I want all changes made offline to reconcile seamlessly upon reconnection via a 3-way merge strategy, so that no data is overwritten or lost.

---

## Implementation Decisions

### 1. 架构分层与模块边界 (Architectural Decisions)
系统严格遵循 Clean Architecture 原则，将职责划分为四个物理解耦的层次：
- **科学计算核心层 (Domain Engine)**：以独立的纯 Swift Package 形式组织，完全剥离所有持久化框架（不依赖 SwiftData、CoreData、UIKit 或任何网络库）。所有公开接口均为无状态纯函数（Pure Functions），接收不可变历史数据输入，产出不可变结果值。
- **本地持久化与数据访问层 (Persistence Layer)**：基于 iOS 17+ SwiftData 构建，管理本地数据库的生命周期、并发读写与版本迁移，向上层提供轻量的查询接口。
- **业务状态与协调层 (Application & ViewModel Layer)**：使用 iOS 17 `@Observable` 宏管理活态会话状态机、组间休息倒计时驱动、云同步调度器以及三方授权接入。
- **UI 呈现与硬件交互层 (Presentation & Native Shell)**：基于 SwiftUI 构建 120Hz 视网膜级交互视图，桥接 CoreHaptics、ActivityKit（灵动岛）、Metal 矢量渲染与 Apple HealthKit。

### 2. 模块定义与接口规范 (Module Interfaces)
- **Progression Module**：公开顶层纯函数接口，接收当前动作配置对象、所属课表上下文以及历史完成记录列表，输出包含建议重量、建议组数、建议每组目标次数、建议原因模板及决策类型的不可变处方结构体。
- **Recovery Module**：公开疲劳与保留力量计算函数，接收历史训练数组、基准时间戳及身体参数选项，返回包含 18 块解剖学肌群数值映射字典；并公开肌群半衰期查询与急性/慢性负荷增益计算器。
- **OneRM Module**：公开标准 Epley 估算与逆向 RIR 估算纯函数，严格处理单侧动作负荷折算。
- **Workout Session State Machine**：管理训练执行态，支持由课表模板或自由训练孵化活态会话，封装组状态切换、热身组自动阶梯重算、超级组指针流转及完成归档逻辑。
- **Sync Coordinator**：管理本地变更队列，监听网络可达性，以增量 Changeset 形式与云端 API 交互，执行基于版本戳的三路合并（3-Way Merge）。

### 3. 数据模式与版本演进 (Schema & Data Modeling)
- 采用 SwiftData `VersionedSchema` 规范，所有持久化实体声明在首个受管版本容器内，禁止非受管的裸模型直接暴露给模型容器。
- **核心实体包含**：
  - 动作定义实体：承载动作标识、标准名称、主要肌群、协同肌群、设备分类及本地动图资源引用；
  - 课表计划实体与关联动作配置实体：承载分化名称、星期归属、进阶策略类型覆盖、单动作组数/次数区间与减载日标记；
  - 训练会话实体、动作执行项实体与组明细实体：以级联结构记录每次实际受训时间戳、真实负荷、实际次数、主观 RIR 及组类型标签；
  - 身体体征快照实体：记录按时间戳归档的体重、腰围、体脂率及静息心率。
- 实体属性采用显式可选链设计，为后续版本字段迭代留足向前与向后兼容缓冲。

### 4. 外部系统与网络契约 (API & External Contracts)
- **多渠道认证契约**：客户端统一定向调用自建用户网关，将各端凭证（Apple Identity Token、微信 OAuth Code、支付宝 Auth Code、手机号与短信验证码）换取标准化颁发的客户端访问 JWT 及刷新令牌。
- **增量同步契约**：同步请求上报客户端最后同步游标（Sync Cursor）与本地未推送变更集合；响应返回服务端最新的合并变更集及新游标。
- **AI Coach 契约**：上报数据严格符合协议契约版本 1，包含受训者脱敏档案、近四周核心负荷与本次会话完成明细；网关返回包含决策列表与总结文案的受约束 JSON 结构，客户端将其渲染为待审批草案。

### 5. 原生交互与视觉渲染规范 (Interaction Decisions)
- **人体热力图渲染**：直接复用解剖学矢量坐标串，封装等比自适应的 SwiftUI 矢量 Shape，通过 Metal 底层着色器对 0.0~1.0 的连续疲劳数值执行色彩插值（绿色至黄色至深红色），并绑定 `.spring(response: 0.35, dampingFraction: 0.7)` 物理弹性动效。
- **灵动岛与锁屏倒计时**：组间休息触发时通过系统 API 启动 Live Activity，向系统宣告结束绝对时间戳，由 iOS 渲染进程独立倒计时驱动，App 切后台或息屏无需占用常驻 CPU。
- **AI 人机闭环审批抽屉**：AI 建议的任何课表修改（加减组、换动作、改重量）必须在原生抽屉（Sheet）中逐行展开为可独立勾选的卡片，受训者明确点击应用后才将变更提交至持久层。

---

## Testing Decisions

### 1. 优秀测试的标准 (What Makes a Good Test)
- **仅测试外部可见行为，杜绝测试内部实现细节**：断言必须基于模块对外的公开输入与输出（例如给定一组历史组数据，断言下一次建议重量与次数），严禁断言私有内部变量或私有函数执行顺序。
- **无状态确定性**：测试环境不得依赖真实时钟、不得依赖未 mock 的网络连通性、不得依赖真实文件系统的不可控残留，所有时间戳与外部依赖均采用显式注入。
- **100% 覆盖关键生理学分支**：自动进阶的每一种达成、失误、连续失误阈值、双倍跳级和减载分支必须具备独立的显式用例。

### 2. 测试模块与测试缝 (Modules & Proposed Testing Seams)

我们全系统仅设计 **四个最高维度的清晰测试缝 (Testing Seams)**：

```
[Seam 1: Domain Engine Seam] ◄── 纯输入输出，零依赖，高密度单测
      │
[Seam 2: Model Context Storage Seam] ◄── 基于内存 SwiftData 容器的 CRUD 与级联操作
      │
[Seam 3: Session State Machine Seam] ◄── 动作打勾、倒计时触发、进阶应用的业务流转
      │
[Seam 4: Sync & Network Transport Seam] ◄── 增量变更合并与 API 契约协议模拟
```

- **Seam 1：纯函数领域引擎缝 (GymEngine Pure Seam)**：
  - 测试目标：`ProgressionEngine`、`RecoveryEngine`、`OneRMEngine`。
  - 测试形式：极速 XCTest 单元测试，秒级运行成百上千个数学断言。
- **Seam 2：内存模型上下文测试缝 (SwiftData In-Memory Seam)**：
  - 测试目标：数据持久化、查询谓词（Predicates）及关系级联删除。
  - 测试形式：初始化 `ModelConfiguration(isStoredInMemoryOnly: true)`，在纯内存环境中验证实体存储与版本迁移逻辑。
- **Seam 3：训练会话状态机测试缝 (Session ViewModel Seam)**：
  - 测试目标：执行一次训练过程中，组状态流转、热身组阶梯联动、灵动岛属性装配及训练完成结算。
  - 测试形式：模拟真实用户点击打勾事件，断言会话产出的历史快照是否准确无损。
- **Seam 4：网络传输协议测试缝 (Mock Transport Seam)**：
  - 测试目标：多端同步合并、登录凭证交换及 AI Coach 契约解析。
  - 测试形式：注入 `URLProtocol` 或 Mock 网络客户端，验证脱机、重试与变更合并正确性。

### 3. 代码库既有参考与先行范例 (Prior Art)
- `dayup/frontend/src/lib/progression.test.js`：涵盖线性、双重进阶、Epley 减载的完整断言用例；
- `dayup/frontend/src/lib/progression.triple.test.js`：涵盖三重进阶每组独立爬次数与加组逻辑；
- `dayup/frontend/src/lib/recovery.test.js`：涵盖 18 肌群半衰期衰减与 ACWR 急性慢性负荷动态放大的数值验证；
- `dayup/frontend/src/lib/onerm.test.js`：涵盖次极限负荷反推 1RM 的精度用例。
所有上述成熟用例将在 **Seam 1** 中逐一转译为对应的 Swift XCTest 用例。

---

## Out of Scope

为保证首发商业版本的交付质量与心流聚焦，以下特性明确排除在本规格之外：
1. **多人在屏社交与训练动态广场**：不构建类似 Instagram / Strava 的公开信息流社区，应用定位于极致个人力量追踪。
2. **Apple Watch 独立无手机训练端**：首期聚焦 iPhone + 灵动岛 + 锁屏实时活动；手表端作为 WatchConnectivity 的远程打勾控制器列入后续演进，不包含在当前实现。
3. **平板大屏横屏定制布局**：应用在 iPad 上以居中兼容模式运行，首期不开发多列分栏的专用 iPadOS 布局。
4. **视频动作自动 AI 姿态识别**：不调用手机摄像头做实时骨骼点动作纠错，AI Coach 仅聚焦运动处方、负荷规划与疲劳周期管理。
5. **海外小众第三方登录**：暂不集成 Google、Facebook 等登录，海外仅保留 Sign in with Apple。

---

## Further Notes

1. **执行秩序纪律**：必须严格执行“算法先跑通、再建模型、后写界面”的开发节奏。任何未通过单元测试的进阶逻辑代码，严禁进入 UI 装配阶段。
2. **Swift 现代语法红线**：根据架构决策，主工程严格基于 iOS 17+ 标准构建，全面使用 `@Observable` 宏与 `NavigationStack`，杜绝任何已废弃的旧版本 API。
3. **动作素材合规**：动作演示动图在开发联调期间采用预制占位符与本地提取包，商业上架前提审阶段需确保动作图源符合商业发布授权准则。
