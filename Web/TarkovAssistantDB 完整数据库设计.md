
## 一、 数据库基础信息
| 配置项 | 取值 | 说明 |
|--------|------|------|
| 数据库名 | `TarkovAssistantDB` | 业务标识命名 |
| 初始版本号 | `1` | 结构变更时版本号递增 |
| 升级策略 | 版本比对+增量迁移 | 避免全量数据重建 |
| 核心特性 | 单库多表、索引优先、状态分离 | 兼顾性能与扩展性 |

## 二、 核心对象仓库设计（含索引&约束）
### 1.  `keys` 仓库（钥匙数据）
| 字段名 | 类型 | 主键/索引 | 约束/说明 |
|--------|------|-----------|-----------|
| `id` | `string` | ✅ 主键（UUID） | 唯一标识，避免名称重复 |
| `name` | `string` | ✅ 唯一索引 | 钥匙名称，全局搜索字段 |
| `mapName` | `string` | ✅ 普通索引 | 所属地图名称，用于地图筛选 |
| `floor` | `number` | - | 钥匙对应楼层 |
| `x` | `number` | - | 坐标X值 |
| `y` | `number` | - | 坐标Y值 |
| `recommend` | `boolean` | ✅ 普通索引 | 是否推荐钥匙 |
| `taskNeed` | `boolean` | ✅ 普通索引 | 是否为任务需求钥匙 |
| `version` | `number` | ✅ 普通索引 | 数据版本号，增量更新用 |

**存储示例**
```javascript
{
  id: "key_001",
  name: "工厂办公室钥匙",
  mapName: "工厂",
  floor: 1,
  x: 125,
  y: 89,
  recommend: true,
  taskNeed: true,
  version: 1
}
```

### 2.  `tasks` 仓库（任务数据）
| 字段名               | 类型                                                                          | 主键/索引                | 约束/说明               |
| ----------------- | --------------------------------------------------------------------------- | -------------------- | ------------------- |
| `id`              | `string`                                                                    | ✅ 主键（UUID）           | 任务唯一标识              |
| `name`            | `string`                                                                    | ✅ 唯一索引               | 任务名称，全局搜索字段         |
| `maps`            | `Array<{name:string, coordinates:Array<{floor:number,x:number,y:number}>}>` | ✅ 索引 `maps.name`（多值） | 关联地图及坐标，支持地图筛选      |
| `tags`            | `Array<string>`                                                             | ✅ 多值索引               | 任务标签（击杀/寻物等），支持标签筛选 |
| `trader`          | `string`                                                                    | ✅ 普通索引               | 发布商人，支持商人筛选         |
| `preTasks`        | `Array<{id:string,name:string}>`                                            | -                    | 前置任务列表              |
| `postTasks`       | `Array<{id:string,name:string}>`                                            | -                    | 后置任务列表              |
| `taskNeedRaid`    | `Array<{name:string,count:number}>`                                         | -                    | 任务需要（战局带出物品）        |
| `taskNeedNotRaid` | `Array<{name:string,count:number}>`                                         | -                    | 任务需要（非战局带出物品）       |
| `version`         | `number`                                                                    | ✅ 普通索引               | 数据版本号               |

**核心优化**：任务物品需求直接存储在本表，避免跨表关联查询；`completed` 状态不存储，移至 `userStates` 仓库。

### 3.  `bullets` 仓库（子弹数据）
| 字段名 | 类型 | 主键/索引 | 约束/说明 |
|--------|------|-----------|-----------|
| `id` | `string` | ✅ 主键（UUID） | 子弹唯一标识 |
| `name` | `string` | ✅ 唯一索引 | 子弹名称，全局搜索字段 |
| `caliber` | `string` | ✅ 普通索引 | 子弹口径，支持口径筛选 |
| `fleshDmg` | `number` | ✅ 普通索引 | 肉伤值，坐标系Y轴 |
| `armorPen` | `number` | ✅ 普通索引 | 穿甲值，坐标系X轴 |
| `version` | `number` | ✅ 普通索引 | 数据版本号 |

### 4.  `items` 仓库（物品数据）
| 字段名 | 类型 | 主键/索引 | 约束/说明 |
|--------|------|-----------|-----------|
| `id` | `string` | ✅ 主键（UUID） | 物品唯一标识 |
| `name` | `string` | ✅ 唯一索引 | 物品名称，全局搜索核心字段 |
| `category` | `string` | ✅ 普通索引 | 物品分类（耗材/武器等），支持分类筛选 |
| `volume` | `number` | - | 物品体积 |
| `value` | `number` | ✅ 普通索引 | 物品当前价格（每日更新） |
| `valuePerVolume` | `number` | ✅ 普通索引 | 单位体积价值（`value/volume`，预计算存储） |
| `version` | `number` | ✅ 普通索引 | 数据版本号 |

**核心优化**：移除原物品中任务/藏身处需求字段，需求完全由 `tasks`/`hiddens` + `userStates` 计算生成，避免数据冗余。

### 5.  `hiddens` 仓库（藏身处数据）
| 字段名 | 类型 | 主键/索引 | 约束/说明 |
|--------|------|-----------|-----------|
| `id` | `string` | ✅ 主键（UUID） | 藏身处模块唯一标识 |
| `name` | `string` | ✅ 唯一索引 | 模块名称（武器工作台等），全局搜索字段 |
| `levels` | `Array<{level:number, needs:Array<{name:string,count:number}>, raid:boolean}>` | - | 各等级需求：`level`等级/`needs`物品清单/`raid`是否需战局带出 |
| `maxLevel` | `number` | - | 模块最高等级，用于快速判断是否满级 |
| `version` | `number` | ✅ 普通索引 | 数据版本号 |

**核心优化**：扁平化等级需求结构，`raid` 字段直接标记该等级需求是否需战局带出，简化计算逻辑。

### 6.  `userStates` 仓库（用户本地状态）
| 字段名 | 类型 | 主键/索引 | 约束/说明 |
|--------|------|-----------|-----------|
| `id` | `string` | ✅ 主键（固定值 `user_state_001`） | 单条记录存储所有用户状态 |
| `taskCompleted` | `Record<string, boolean>` | - | 任务完成状态映射，`key=taskId, value=是否完成` |
| `hiddenCurrentLevels` | `Record<string, number>` | - | 藏身处模块当前等级映射，`key=hiddenId, value=当前等级` |
| `lastUpdateTime` | `Record<string, number>` | - | 各仓库最后更新时间戳，`key=仓库名, value=时间戳` |
| `searchHistory` | `Array<string>` | - | 用户搜索历史，用于联想提示 |

**核心作用**：实现基础数据与用户状态完全分离，支持多端同步（需手动导入导出）。

## 三、 索引设计总览（性能核心）
| 索引类型 | 涉及仓库                                       | 索引字段                                | 核心作用           |
| ---- | ------------------------------------------ | ----------------------------------- | -------------- |
| 唯一索引 | `keys`/`tasks`/`bullets`/`items`/`hiddens` | `name`                              | 保证名称唯一，加速名称搜索  |
| 普通索引 | `keys`                                     | `mapName`/`recommend`/`taskNeed`    | 支持地图、推荐、任务需求筛选 |
| 普通索引 | `tasks`                                    | `trader`/`version`                  | 支持商人筛选、增量更新    |
| 多值索引 | `tasks`                                    | `maps.name`/`tags`                  | 支持多地图、多标签筛选    |
| 普通索引 | `bullets`                                  | `caliber`/`fleshDmg`/`armorPen`     | 支持口径、肉伤、穿甲筛选   |
| 普通索引 | `items`                                    | `category`/`value`/`valuePerVolume` | 支持分类、价格、单位价值筛选 |
| 普通索引 | 所有仓库                                       | `version`                           | 支撑全仓库增量更新策略    |

## 四、 数据更新策略
### 1.  每日物品价格更新
1.  服务端下发**增量价格包**：仅含 `itemId + newPrice`
2.  客户端开启事务，批量更新 `items` 仓库的 `value` 字段
3.  同步更新 `valuePerVolume` 字段（`newPrice/volume`）
4.  更新 `userStates.lastUpdateTime.items` 为当前时间戳

### 2.  全仓库增量更新
1.  服务端下发**版本比对包**：含各仓库最新版本号 + 增量数据（版本号高于本地的记录）
2.  客户端按仓库遍历，比对本地 `version`，仅更新差异记录
3.  若存在删除数据，服务端下发删除列表，客户端执行 `delete` 操作
4.  更新对应仓库的 `lastUpdateTime`
5.  **触发全量需求计算**，刷新内存中的 `globalItemNeedMap`

### 3.  用户状态更新
- 用户修改任务完成状态/藏身处等级时，直接更新 `userStates` 仓库
- 触发**增量需求计算**，仅更新受影响的物品需求数据，前端视图实时刷新

## 五、 全局搜索实现方案
1.  **索引依赖**：基于所有仓库的 `name` 字段唯一索引
2.  **搜索逻辑**
    - 输入防抖（300ms），关键词转小写
    - 并行查询 `keys`/`tasks`/`bullets`/`items`/`hiddens` 仓库的 `name` 字段
    - 前缀匹配优先，模糊匹配补充，按匹配度排序
    - 结果缓存 5 分钟，关联展示物品需求总量（若有）
3.  **联想提示**：结合 `userStates.searchHistory`，优先展示历史搜索关键词

我可以帮你编写**数据库初始化与版本升级的 IndexedDB 操作代码**，需要吗？
用户:
给出完整的数据库设计
内容由 AI 生成