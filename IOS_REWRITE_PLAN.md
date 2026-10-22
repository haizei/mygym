# MyGym 原生 iOS 重写与商业化落地完整开发计划

> **项目目标**：将 `openGym` 的科学内核（进阶策略、ACWR 疲劳模型、1300+ 动作库）与现代原生 iOS（SwiftUI + SwiftData + 灵动岛 + 原生矢量热力图 + AI Coach）深度融合，打造面向 App Store 上架的商业级力量训练应用。  
> **工程落地根目录**：`/Users/haizei/work/AI/program/mygym/ios/`  
> **参考与抽取源**：`/Users/haizei/work/AI/program/mygym/dayup/`  
> **进度只改一处**：`wiki/topics/MyGym原生iOS重写.md`（知识库勾选页）。本文件是规格，勾选以那一页为准。  
> **执行标准**：Karpathy 行为守则（先思后行、极简至上、外科手术式改动、目标导向验证）。

---

## 一、系统总体架构蓝图

```
┌────────────────────────────────────────────────────────────────────────┐
│                        MyGym iOS Client (iOS 17+)                      │
│                                                                        │
│  [UI 呈现层 - SwiftUI 120Hz & 质感体验]                                  │
│  ├── 训练工作台 (WorkoutView) + 触感震动 (CoreHaptics)                    │
│  ├── 纯矢量人体热力图 (BodyMapView / Metal GPU 渲染)                     │
│  ├── 锁屏与灵动岛实时倒计时 (ActivityKit / Live Activities)               │
│  ├── 课表与动作库 (RoutineEditView / ExerciseLibraryView)                 │
│  └── 智能教练交互舱 (AICoachView - 结构化人机闭环审批)                     │
│                                                                        │
│  [状态管理与应用层 - @Observable ViewModels]                             │
│  ├── WorkoutSessionModel (训练执行态状态机、超级组流转)                    │
│  ├── AuthViewModel (Apple / 微信 / 支付宝 / 手机号四合一登录)             │
│  └── SyncCoordinator (离线优先冲突合并协调器)                             │
│                                                                        │
│  [科学计算核心层 - GymEngine (纯 Swift 独立 Package, 100% 覆盖单测)]      │
│  ├── ProgressionEngine (线性 / Greyskull LP / 双重 / 三重 / Deload)     │
│  ├── RecoveryEngine (ACWR 急性慢性负荷 / 18肌群独立半衰期衰减)            │
│  └── OneRMEngine (Epley 1RM 估算 / 次极限疲劳反推)                       │
│                                                                        │
│  [本地持久化与硬件层 - Local-First SwiftData]                            │
│  ├── SwiftData 实体模型 (Workout, Set, Routine, Exercise, Metrics)      │
│  ├── Apple HealthKit (双向读取体重/心率，写入力量体能训练)                │
│  └── 动作演示动图/WebP 本地二级缓存引擎 (Disk + Memory LRU)              │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ HTTPS + WSS (JWT 鉴权)
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                      MyGym Cloud Services (自建后端)                   │
│                                                                        │
│  ├── 统一账号服务 (Auth): 手机短信验证码 / 微信 OAuth / 支付宝 / Sign in Apple│
│  ├── 增量同步网关 (Sync): 3-Way Merge 字段变更版本向量合并协议            │
│  └── AI Coach 网关 (Proxy): 校验 VIP 订阅、组装训练工程 Prompt、防盗 Key   │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 二、开发分期与详细任务排期表（里程碑驱动）

全案划分为 **5 个演进阶段**，每一步均定义严格的“验证标准（Verification Criteria）”，确保每一行代码都可测、可用、稳步推进。

---

### 阶段一：科学大脑移植（GymEngine 纯 Swift 包与全量单测）

**目标**：完全不碰任何 UI 和数据库，先把 openGym 最核心的科学算法翻译为纯 Swift，单测 100% 跑绿，奠定不偏航的计算基座。

* **Step 1.1**：在 `ios/Packages/GymEngine` 初始化独立 Swift Package。
  * 提取并建立 `ExerciseIdentifier`、`RepRange`、`Prescription` 等领域值对象。
  * 对应参考：`dayup/frontend/src/lib/rep-range.js`。
  * *验收标准*：`swift package dump-package` 通过。

* **Step 1.2**：移植 1RM 估算引擎（`OneRMEngine.swift`）。
  * 实现 Epley 公式：`weight * (1 + reps / 30.0)`；
  * 处理单侧动作（Per-side）次数减半规则与负荷折算；
  * 对应参考：`dayup/frontend/src/lib/onerm.js`。
  * *验收标准*：移植 `onerm.test.js` 中的测试用例，XCTest 单元测试全部通过。

* **Step 1.3**：移植自动进阶与减载策略引擎（`ProgressionEngine.swift`）。
  * 编写纯函数 `nextPrescription(history:config:routine:) -> Prescription`；
  * 落地五大策略：`off`（手动）、`linear`（线性加重）、`greyskull`（力竭跳级/单次减载）、`double`（区间爬次数）、`triple`（次数→组数→重量）；
  * 落地防误判的停滞检测器（`stallCount`）与 Epley 90% 候选权重选（`selectDeloadCandidate`）；
  * 对应参考：`dayup/frontend/src/lib/progression.js`。
  * *验收标准*：对齐 `progression.test.js` 与 `progression.triple.test.js` 所有的断言分支，XCTest 全绿。

* **Step 1.4**：移植 ACWR 疲劳与恢复模型（`RecoveryEngine.swift`）。
  * 实现有效硬组（Effective Hard Set）折算与 12 组上限封顶；
  * 实现 7 天急性 / 28 天慢性（ACWR）负荷增益比率（0.5x ~ 2.0x 动态放大）；
  * 实现 18 个主要肌群的不同指数半衰期衰减（手臂 24h、躯干 30h、腿部 48h）；
  * 饱和曲线映射：`1 - exp(-v)` 得出 0~1 的平滑疲劳值；
  * 对应参考：`dayup/frontend/src/lib/recovery.js`。
  * *验收标准*：对齐 `recovery.test.js` 单测用例，验证时间流逝后疲劳值衰减数学精度 < 1e-6。

---

### 阶段二：数据模型与底层架构（SwiftData 实体与资产管线）

**目标**：建立 Local-First 的本地数据库、设计平滑的版本迁移机制，并抽取原项目 1300+ 动作库资产。

* **Step 2.1**：创建主工程 `MyGym.xcodeproj`（iOS 17+ Deployment Target）。
  * 引入本地依赖 `GymEngine`；
  * 开启 SwiftUI 现代化生命周期。
  * *验收标准*：模拟器无报错启动空白页。

* **Step 2.2**：设计 SwiftData 持久化模型（`SchemaV1`）。
  * 实体包括：
    * `@Model final class ExerciseDefinition`（动作库）
    * `@Model final class RoutinePlan`（课表定义）
    * `@Model final class RoutineExercisePlan`（课表内动作配置）
    * `@Model final class WorkoutSession`（单次训练记录）
    * `@Model final class WorkoutEntryRecord`（训练动作项）
    * `@Model final class WorkoutSetRecord`（训练组细项：w, r, rir, done, type）
    * `@Model final class BodyMetricSnapshot`（体重、腰围、体脂、心率快照）
  * 使用 `VersionedSchema` 封装，为后续 App Store 更新预留无损迁移能力。
  * *验收标准*：编写 Storage 集成测试，验证 10,000 条组数据在 30ms 内极速写入与读取。

* **Step 2.3**：动作库提取与本地数据播种（Asset Pipeline）。
  * 编写 Node/Python 抽取脚本，将 `dayup/frontend/src/lib/exercises-data.js` 清洗为压缩的 `BuiltinExercises.json`，打入 iOS App Bundle；
  * 建立轻量级图片/动图缓存系统（支持从 CDN 懒加载下载，并在本地沙盒永久离线保存）。
  * *验收标准*：首次启动 App 时，自动将 1300+ 动作秒级播种至本地数据库，支持按肌群和器械离线搜索。

---

### 阶段三：核心训练体验打磨（Workout 核心流、触感与灵动岛）

**目标**：还原并超越 openGym 最核心的训练与课表流，做出纯原生 iOS 的顶级敲击感和锁屏体验。

* **Step 3.1**：今日训练工作台（`WorkoutView` 与 `WorkoutSessionModel`）。
  * 状态机：支持未开始、进行中、已暂停、已完成；
  * 动作卡片：展示动作演示图、进阶提示条（Progline），自动调用 `GymEngine` 预填建议的重量与次数；
  * 组记录打勾行（SetRowView）：支持热身组（W）、正式组、递减组（D）、力竭组（F）标记；
  * 触感引擎集成：点击打勾触发 `UIImpactFeedbackGenerator(style: .medium)`；
  * 对应参考：`dayup/frontend/src/views/Workout.jsx`。
  * *验收标准*：完整模拟一次包含 4 动作 16 组的训练记录流程，交互流畅无掉帧。

* **Step 3.2**：组间休息系统与灵动岛（Live Activities & ActivityKit）。
  * 编写独立 Target：`MyGymWidgetsExtension`；
  * 定义 `RestTimerAttributes` 实时活动；
  * 用户每打勾完成一组，App 自动触发组间休息倒计时（如 90 秒），锁屏界面展示倒计时圆环，iPhone 灵动岛常驻倒计数字；
  * 倒计时最后 3 秒触发系统触感提醒，结束时伴随悦耳的原生铃声。
  * *验收标准*：锁屏状态下无需点亮屏幕，倒计时精确归零并在灵动岛展开。

* **Step 3.3**：课表管理与编排（`RoutineListView` & `RoutineEditorView`）。
  * 支持自定义训练分化（推拉腿 PPL、上下肢、全身等）；
  * 动作拖拽排序（SwiftUI `.onMove` 流畅手势）；
  * 单动作级别的进阶策略选择抽屉（即用户截图中的 Progression 选项表）。
  * 对应参考：`dayup/frontend/src/views/RoutineEdit.jsx`。
  * *验收标准*：支持任意增删动作、修改目标区间、设置 Deload Routine 开关。

---

### 阶段四：原生矢量热力图与统计大盘（BodyMap & Charts）

**目标**：用纯原生 Metal 硬件加速重写人体疲劳图，提供高大上的身体恢复仪表盘。

* **Step 4.1**：SVG 矢量坐标转 SwiftUI Native Shape。
  * 提取 `dayup/frontend/src/components/BodyMap.jsx` 中 18 块肌肉（正面/背面）的精确 Path 字符串；
  * 编写 `VectorBodyShape` 视图组件，自动自适应容器尺寸保持解剖学等比；
  * 对应参考：`dayup/frontend/src/components/BodyMap.jsx`。
  * *验收标准*：在不同尺寸 iPhone 屏幕上渲染，边缘 100% 矢量平滑锐利。

* **Step 4.2**：肌肉疲劳色彩连续插值与微交互。
  * 结合 `RecoveryEngine` 算出的 0~1 疲劳值，通过颜色插值实现色彩平滑渐变：
    * `< 0.25`：暗绿 / 准备就绪（Ready）；
    * `0.25 ~ 0.50`：暖橙 / 恢复中（Recovering）；
    * `> 0.50`：亮红 / 深度疲劳（Fatigued）；
  * 手指轻点任意肌群：伴随 Spring 弹簧动效轻微浮起高亮，弹出浮层显示该肌群最后受训时间、当前保留力量及关联训练动作。
  * *验收标准*：通过单元测试构造假数据，热力图在点击与切换日期时动画达到 120Hz 满帧。

* **Step 4.3**：数据统计分析大盘（`StatsView`）。
  * 基于 iOS 16+ `Swift Charts` 框架实现容量柱状图与 1RM 力量突破曲线；
  * 记录并展示身体成分变化：体重趋势线、腰围追踪折线、静息心率趋势。
  * *验收标准*：图表支持长按十字游标手势查看特定日期的精确数值。

---

### 阶段五：商业化闭环（多渠道账号体系 + 增量云同步 + AI Coach 网关）

**目标**：补齐面向国内 App Store 上架的核心商业服务组件。

* **Step 5.1**：多渠道账号认证体系（`AuthModule`）。
  * 客户端原生接入：
    1. **Sign in with Apple**（`AuthenticationServices` 原生无缝集成）；
    2. **微信 OpenSDK**（唤起微信授权，获取临时 Auth Code）；
    3. **支付宝登录 SDK**（唤起支付宝授权票据）；
    4. **手机号短信验证码**（自建验证码发送与校验流程）。
  * 云端统一用户中心：将多种三方凭证归一化绑定到唯一 `UserUID`，客户端持久化 Keychain Token。
  * *验收标准*：真机测试 Apple 登录、微信登录、手机号登录，任意一种均可正常换取 Token 并在设备间同步身份。

* **Step 5.2**：增量数据云同步服务（`CloudSyncEngine`）。
  * 基于本地 SwiftData 的修改时间戳，封装轻量增量 Changeset；
  * 登录后首次拉取全量合并；日常训练结束后在后台静默异步提交变更；
  * 保证本地离线为王（Offline-First），即使断网几个月，联网后依然能平滑合流。
  * *验收标准*：在设备 A 记录一次训练，设备 B 打开后 2 秒内无感呈现。

* **Step 5.3**：AI Coach 原生功能与安全网关对接。
  * 移植原版结构化交互协议（`coach_contract: 1`），包含：
    * 训练前：周期调课建议、痛点替换动作推荐；
    * 训练后：单次训练复盘（Debrief）与疲劳预警。
  * 客户端构建严密的人机交互审批流：AI 提出的计划修改以「待确认卡片（Pending Card）」呈现，用户逐项勾选应用后才写入 SwiftData；
  * 云端部署轻量 API 网关（校验用户身份与订阅，组装 Prompt，请求后端大模型）。
  * *验收标准*：端到端跑通“请求 AI 调整课表 → AI 返回结构化变更 → 用户审核确认 → 本地课表自动更新”全流程。

* **Step 5.4**：Apple HealthKit 双向联动。
  * 权限申请与配置；
  * 读：自动抓取每日早晨的体重与静息心率，写入 BodyMetricSnapshot；
  * 写：训练结束时，将本次力量训练以 `HKWorkoutActivityType.traditionalStrengthTraining` 写入苹果健身圆环，记录消耗卡路里与持续时间。
  * *验收标准*：在 iOS 健康 App 中能清晰看到 MyGym 记录的有效运动记录。

---

## 三、目录结构规划（落地在 `mygym/ios/`）

```text
mygym/
├── CONTEXT.md                            # 统一领域模型词典（已建立）
├── IOS_REWRITE_PLAN.md                   # 本规划指南文件
├── docs/
│   └── adr/
│       └── 0001-architecture-and-storage.md  # 核心架构决策记录（已建立）
├── dayup/                                # openGym 原版代码（参考提取源）
└── ios/                                  # 新建原生工程根目录
    ├── MyGym.xcodeproj
    ├── MyGym/
    │   ├── App/                          # App 入口、生命周期、全局容器
    │   ├── Models/                       # SwiftData 实体定义与 Schema 迁移
    │   ├── Views/                        # 核心视图
    │   │   ├── Workout/                  # 今日训练、动作卡片、打勾行
    │   │   ├── Routines/                 # 课表管理、进阶规则配置弹窗
    │   │   ├── BodyMap/                  # 纯原生矢量人体热力图 Shape
    │   │   ├── Stats/                    # Swift Charts 数据统计与 PR 墙
    │   │   ├── Coach/                    # AI 教练建议流与确认抽屉
    │   │   └── Auth/                     # 苹果/微信/支付宝/手机号登录界面
    │   ├── ViewModels/                   # 业务状态机 (@Observable)
    │   ├── Services/                     # HealthKit、本地文件缓存、音频触感
    │   └── Resources/                    # Assets、内置动作库 JSON、本地化翻译
    ├── MyGymWidgets/                     # 灵动岛 (Live Activity) 与小组件 Extension
    └── Packages/
        └── GymEngine/                    # 纯 Swift 算法库 (无状态纯函数)
            ├── Sources/GymEngine/        # Progression, Recovery, OneRM, RepRange
            └── Tests/GymEngineTests/     # 100% 覆盖率的 XCTest 单元测试
```

---

## 四、推进原则（Karpathy Guidelines 纪律）

1. **先做纯函数，不碰 UI**：阶段一必须在没有 Xcode 视图代码的纯 Swift Package 下把单测全部跑绿，确保计算逻辑 100% 正确。
2. **严禁在 ViewModel 里发明算法**：任何关于“加多少重量”、“怎么减载”、“肌肉疲劳度多少”的逻辑必须 100% 来自 `GymEngine`，ViewModel 只做纯数据组装。
3. **永远保证离线可用**：所有网络请求（登录、同步、AI）失败时，App 的记录、进阶推导、打勾、倒计时都必须丝滑无阻，绝对不允许因断网出现加载菊花或阻塞卡死。
