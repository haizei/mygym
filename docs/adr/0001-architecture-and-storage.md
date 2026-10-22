# ADR 0001: MyGym 原生 iOS 客户端架构与多端同步方案

- **状态**: Accepted
- **日期**: 2026-10-22
- **决策者**: 架构与产品团队

---

## 1. 背景与上下文 (Context)

原开源项目 `openGym` 是一个基于 React 19 + Node.js 极简后端 + 本地单一 JSON (`gym_state_v1`) 的自托管 PWA，通过 Capacitor 封装为移动端外壳。

当前业务目标是将该项目**用原生 iOS (SwiftUI) 重写，打造面向 App Store 商业化发布、支持大众用户的高端健身应用**。需求特征包括：
1. **极致的原生质感**：组间休息需要支持**灵动岛 / 锁屏实时活动（ActivityKit）**、120Hz 原生动画、CoreHaptics 细腻震动反馈、Apple HealthKit 双向健康联动；
2. **商业化账号体系**：支持中国大陆主流的多渠道快捷登录（微信登录、支付宝登录、手机号验证码）以及苹果强制的 Sign in with Apple；
3. **海量用户与未来多端拓展**：长期用户积累成千上万条组数据，且未来可能拓展 Android、小程序或网页端管理后台。

---

## 2. 面临的决策问题 (Decision Drivers)

- **纯单文件 JSON** 在商业 iOS 上遇到无法跨越的系统级限制：桌面小组件（WidgetKit）和灵动岛实时活动（ActivityKit）受系统 30MB 内存严格配额限制，反序列化单一大 JSON 必然引发频繁 Jetsam 杀进程闪退；且无法做版本迁移（Migration）和按需分页加载。
- **纯 iCloud (CloudKit) 方案** 仅适用于单一 Apple 生态，与第三方登录（微信、支付宝、手机号）和未来多平台体系严重割裂，无法在 Android 或后台进行用户凭证打通与会员订阅风控。
- **纯云端在线方案** 在健身房地下室弱网或无网络环境下极易导致白屏、丢失打勾记录，彻底破坏受训心流。

---

## 3. 决策内容 (Decision)

我们决定采用：**「Local-First 本地优先 + iOS 17 SwiftData + 纯函数领域引擎 + 自建云端同步网关」** 的现代化混合架构。

### 3.1 客户端架构（分层与解耦）
1. **核心计算引擎（GymEngine）**：
   - 将 openGym 的算法（Progression 进阶、Recovery 疲劳衰减、OneRM 估算）以**纯 Swift Package** 独立封装；
   - 100% 保持纯函数、无状态计算；不依赖 SwiftData、不依赖任何三方库；
   - 配齐 100% 覆盖率的 XCTest 单元测试，确保训练科学严谨性。
2. **本地持久化与响应式视图（SwiftData + SwiftUI）**：
   - 采用 iOS 17+ SwiftData 作为本地数据库，负责毫秒级零延迟读取、小组件低开销按需查询、安全的数据版本迁移（VersionedSchema）；
   - 使用 iOS 17 `@Observable` 宏管理会话状态机，驱动 120Hz ProMotion 界面与 CoreHaptics 触感。

### 3.2 账号与云端同步（Cloud Sync Architecture）
1. **多渠合一统一账号体系**：
   - 建立自建用户服务（Auth Service），支持 `手机号 + 微信 + 支付宝 + Sign in with Apple` 联合绑定；
   - 客户端登录后分发标准化 JWT，支持安全刷新与注销。
2. **增量增量同步协议（Change-based Sync）**：
   - 本地 SwiftData 记录字段级或实体级修改版本戳（Timestamp / Revision）；
   - 离线时在本地极速记录；联网时由客户端向自建云端同步网关提交增量 Changeset，利用类似 openGym 已有的 3-Way Merge 机制做冲突合并；
   - 放弃直接绑定 CloudKit 私有库，完全掌控多端数据与商业化订阅。

### 3.3 AI Coach 云端安全网关
- AI Coach 作为云端微服务部署，作为反向代理与大模型交互；
- iOS 客户端上报脱敏后的 JSON 训练上下文（`coach_contract: 1`），云端安全网关校验 VIP 订阅后注入 Prompt 与私有 API Key，返回结构化训练调整草案，客户端人机确认后入库。

---

## 4. 影响与结果 (Consequences)

### 正向收益 (Positive)
- **极致的离线体验**：健身房哪怕完全无信号，所有训练记录、倒计时、进阶推导完全在手机本地以 0 毫秒延迟运行；
- **系统级特性满血释放**：灵动岛、桌面小组件、Apple Watch 独立端可以仅按需查询 SwiftData，内存占用 <5MB，绝不被 iOS 强杀；
- **全平台商用自由**：账号与数据沉淀在自建服务，完全解耦苹果生态锁死，后续上线 Android 或小程序无任何历史包袱；
- **会员商业化底座稳固**：AI 接口与会员权益在自建网关鉴权，无法被破解绕过。

### 妥协与代价 (Trade-offs)
- 需要团队搭建与维护基础的云端 API（用户服务 + 同步接口 + AI 网关），无法像纯单机 App 那样实现零服务器成本；
- 需实现本地 SwiftData 变更日志与云端 JSON 结构之间的映射与合并逻辑。
