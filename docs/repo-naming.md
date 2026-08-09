# NexusAir-Technologies 仓库命名规范

> 版本：1.0 | 生效日期：2026-08-09
> 适用范围：组织内**所有新建** GitHub 仓库。存量仓库已于 2026-08-09 完成首批迁移（10 个），仅 PX4 相关与模板仓库保留原名，见「八、存量仓库迁移记录」。

---

## 一、总则

1. 仓库名由 **小写分类前缀 + 下划线 + 驼峰实体名（+ 可选驼峰变体）** 组成。
2. 命名风格：**前缀用全小写，实体用 UpperCamelCase（驼峰）**，两者之间用单个下划线 `_` 分隔。
3. **禁止**使用连字符 `-`、空格；下划线**仅允许**出现在前缀之后，实体内部不使用下划线。
4. 仓库名应描述「**做什么**」（领域语义），而非「用什么做」（技术栈）。
5. 新仓库创建时按本规范命名。存量仓库已于 2026-08-09 完成首批迁移（10 个仓库），仅 PX4 相关（`PX4-Autopilot_VectorMC`、`Vector_MC`、`PX4-H1`）与模板仓库 `tool-repo-template` 保留原名。

## 二、命名格式

```
{prefix}_{Entity}[{Variant}]
```

| 字段 | 必填 | 写法 | 说明 | 示例 |
|------|------|------|------|------|
| `prefix` | 是 | 全小写 | 分类前缀，见下表 | `fw` |
| `_` | 是 | 下划线 | 前缀与实体间的唯一分隔符 | `_` |
| `Entity` | 是 | UpperCamelCase | 语义主体，多词驼峰连接 | `Stm32Guidance` |
| `Variant` | 否 | UpperCamelCase | 同实体的平台/代际/用途分支 | `Track` |

变体直接拼接在实体后，**不再使用下划线**，靠驼峰划分词边界。

### 2.1 命名示例

- `fw_Stm32Guidance`
- `fw_RK3588Seeker`
- `fw_Stm32GuidanceTrack`
- `px4_H1`
- `px4_VectorMC`
- `tool_RepoTemplate`
- `org_WeeklyReports`
- `agent_Image2MCP`

## 三、分类前缀表（强制）

| 前缀 | 类别 | 典型内容 | 与现有仓库对照 |
|------|------|----------|----------------|
| `fw` | 固件 / 嵌入式 | 单片机固件、视觉导引头、制导中间件、传感器节点 | `galvanometer` → `fw_Galvanometer` |
| `px4` | PX4 自驾仪固件 | 基于 PX4 的 fork / 定制固件 | `PX4-H1` → `px4_H1` |
| `tool` | 工具 / 库 / 模板 | CLI 工具、开发库、仓库模板 | `claude-deepseek` → `tool_ClaudeDeepSeek` |
| `agent` | AI 代理生态 | Claude Code skills、MCP servers、代理配置 | `agent-skills` → `agent_Skills` |
| `ai` | AI / ML 算法与训练 | 强化学习、感知算法、仿真训练 | `isaac_so_arm101_catch` → `ai_IsaacSoArm101Catch` |
| `app` | 业务应用 / 服务 | Web 应用、API 服务、完整系统 | `ai-inventory-system` → `app_AiInventorySystem` |
| `org` | 组织运营 | 周报、规范、wiki、会议记录 | `weekly-reports` → `org_WeeklyReports` |

分类判定优先顺序：**固件 > PX4 > 代理生态 > AI/ML > 应用 > 工具 > 组织运营**。仓库同时跨多类时，取最内层的本质类别（例如 MCP server 服务于代理生态，归 `agent` 而非 `tool`）。

## 四、实体命名规则

1. 用**领域语言**描述功能或对象，避免技术栈词（`Cpp`、`Ts`）。
2. 实体采用 UpperCamelCase，多词驼峰连接，词间无分隔符。
3. **品牌 / 标准缩写保留全大写**：`RK`、`MCP`、`MC`、`AI`、`ML`、`ARM`。例：`fw_RK3588Seeker`、`agent_Image2MCP`、`px4_VectorMC`。
4. **型号 / 代号保留数字**：`RK3588`、`H1` 原样保留：`fw_RK3588Seeker`、`px4_H1`。
5. 连续缩写导致可读性差时，允许按单词驼峰归一（`SO-ARM101` → `SoArm101`）。
6. 避免冗余词：`System`、`Service`、`Platform` 除非确实必要。
7. 域名词（如 `AI`）允许保留在实体中：`app_AiInventorySystem`。

## 五、变体后缀规则

1. 同一实体存在平台、代际或用途分支时，将 `Variant` **直接拼接**在实体后：`fw_Stm32GuidanceTrack`。
2. 多代际可使用数字：`px4_H1V2`。
3. 变体与实体之间**不加下划线**，靠驼峰划分；下划线仅保留在 `{prefix}_` 一处。

## 六、禁止事项

- ❌ 连字符、空格（`fw-stm32-guidance` 不合规）。
- ❌ 前缀后多个下划线（`fw_Stm32_Guidance`、`fw_Stm32Guidance_Track`）。
- ❌ 全 snake_case（`fw_stm32_guidance`）、全小写（`fwstm32guidance`）、全大写（`FW_STM32_GUIDANCE`）。
- ❌ 前缀大写（`Fw_Stm32Guidance`）。
- ❌ 无前缀的裸命名（`galvanometer`）。
- ❌ 前缀混用或重复（`fw_px4_...`、`app_ai_ai_...`）。
- ❌ 以个人 / 群组名作为前缀（`zhangsan_...`）。
- ❌ 以技术栈命名实体（`tool_ReactFrontend`）。

## 七、新建仓库检查清单

创建新仓库前逐项确认：

- [ ] 是否落在某个分类前缀下？分类依据是内容本质而非技术栈？
- [ ] 是否为 `{小写前缀}_` + `{UpperCamelCase}`，前缀后仅一个下划线？
- [ ] 实体名是否语义自明、缩写处理是否符合第四节？
- [ ] 是否有同名 / 近名仓库需区分（改用 Variant）？
- [ ] 与 `tool-repo-template` 的 README / CLAUDE.md 模板是否一致？

## 八、存量仓库迁移记录（2026-08-09）

首批迁移已执行完成，以下 10 个仓库按本规范重命名（GitHub 对旧 URL 自动重定向）：

| 原名称 | 迁移后名称 |
|--------|------------|
| `agent-skills` | `agent_Skills` |
| `fw-rk3588-seeker` | `fw_RK3588Seeker` |
| `fw-stm32-guidance` | `fw_Stm32Guidance` |
| `fw-stm32-guidance_track` | `fw_Stm32GuidanceTrack` |
| `galvanometer` | `fw_Galvanometer` |
| `weekly-reports` | `org_WeeklyReports` |
| `claude-deepseek` | `tool_ClaudeDeepSeek` |
| `image2-mcp` | `agent_Image2MCP` |
| `isaac_so_arm101_catch` | `ai_IsaacSoArm101Catch` |
| `ai-inventory-system` | `app_AiInventorySystem` |

以下仓库保留原名，后续按需评估：

| 仓库名 | 保留原因 |
|--------|----------|
| `PX4-Autopilot_VectorMC` | PX4 相关，暂不迁移 |
| `Vector_MC` | PX4 相关；与 `PX4-Autopilot_VectorMC` 疑似重复，建议评估归档 |
| `PX4-H1` | PX4 相关，暂不迁移 |
| `tool-repo-template` | 组织模板仓，保留原名 |

> 已迁移仓库外部引用需同步更新（README / CI / 文档中的旧链接）；GitHub 为旧 URL 提供重定向。

---

## 附：说明

- **风格来源**：`前缀_驼峰` 兼顾「类别一眼可辨」（前缀）与「产品名可读性」（驼峰实体），下划线仅作为前缀与实体间的清晰边界。
- **GitHub 兼容性**：下划线与大小写混合命名在 GitHub 完全有效；URL 对大小写不敏感，会重定向到创建时的大小写，克隆地址以创建时为准。
- **强制前缀**：仓库名本身即可表达类别，配合 GitHub topic 与 README 三级定位，降低检索与归档成本。
- **存量迁移**：2026-08-09 已完成首批 10 个仓库迁移；PX4 相关与模板仓暂保留原名，后续按需评估。
