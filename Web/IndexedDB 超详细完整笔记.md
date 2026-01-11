
IndexedDB 是浏览器内置的**非关系型、事务型、异步本地数据库**，支持存储大量结构化数据（上限一般为浏览器磁盘配额，远超 `localStorage` 的 5MB 限制），适用于离线应用、数据缓存、复杂前端业务数据存储等场景。

## 一、 核心概念
### 1.  数据库（Database）
- 浏览器中可创建**多个独立数据库**，每个数据库有唯一名称（自定义）。
- 数据库有**版本号**，仅支持**递增升级**（不能降级），版本变更会触发核心的 `onupgradeneeded` 事件（唯一可修改数据库结构的入口）。
- 特点：同源策略限制（同一域名+端口共享数据库），不同源页面无法访问彼此的 IndexedDB 数据。

### 2.  对象仓库（Object Store）
- 相当于关系型数据库的**表**，用于存储同一类结构化数据。
- 每个仓库必须指定 `keyPath`（主键字段，如 `id`），主键值必须唯一；也可设置 `autoIncrement: true` 让主键自增。
- 一个数据库可包含**多个对象仓库**，不同仓库存储不同类型的数据。

### 3.  索引（Index）
- 为仓库的非主键字段创建索引，用于**加速查询**，避免全表扫描（无索引查询会遍历所有数据，性能极差）。
- 索引核心配置：
  - `unique`: 布尔值，是否要求索引字段值唯一（如用户手机号索引设为 `true`）。
  - `multiEntry`: 布尔值，若索引字段是数组，是否为数组每个元素单独建立索引（如标签数组 `["美食", "旅行"]`，开启后可通过单个标签查询）。
- 一个仓库可创建**多个索引**，覆盖所有高频查询字段。

### 4.  事务（Transaction）
- IndexedDB **所有读写操作必须在事务中执行**，保证操作的原子性（要么全部成功，要么全部失败回滚）。
- 事务模式：
  - `readwrite`: 支持增、删、改、查，用于数据写入/更新操作。
  - `readonly`: 仅支持查询，性能高于 `readwrite`，用于纯读取场景。
  - `versionchange`: 特殊模式，仅在版本升级时使用，一般无需手动设置。
- 事务生命周期：操作完成后自动提交，若事务内有异步操作未完成，事务会提前终止导致操作失败。

### 5.  游标（Cursor）
- 用于**遍历对象仓库或索引中的数据**，支持按条件筛选、分页查询，是处理大量数据的核心方式。
- 游标可配合 `IDBKeyRange` 设置查询范围（如查询 `id` 大于 10 且小于 100 的数据）。

### 6.  键范围（IDBKeyRange）
- 定义数据查询的范围，常与游标配合使用，支持以下四种范围：
  - `only(value)`: 匹配唯一值（如 `IDBKeyRange.only("zhangsan")`）。
  - `lowerBound(value, open?)`: 匹配大于等于 `value` 的值，`open=true` 则为大于（不含等于）。
  - `upperBound(value, open?)`: 匹配小于等于 `value` 的值，`open=true` 则为小于（不含等于）。
  - `bound(lower, upper, lowerOpen?, upperOpen?)`: 匹配介于 `lower` 和 `upper` 之间的值。

## 二、 基础操作流程
### 1.  打开/创建数据库
这是 IndexedDB 操作的第一步，无论创建新数据库还是打开已有数据库，都使用 `indexedDB.open()` 方法。
```javascript
let dbInstance = null; // 缓存数据库实例，避免重复打开

/**
 * 打开/创建数据库
 * @param {string} dbName 数据库名称
 * @param {number} version 数据库版本号
 * @returns {Promise<IDBDatabase>} 数据库实例Promise
 */
function openDB(dbName, version) {
  return new Promise((resolve, reject) => {
    // 关闭已有实例，防止版本冲突
    if (dbInstance) {
      dbInstance.close();
    }

    // 核心方法：打开数据库
    const request = indexedDB.open(dbName, version);

    /**
     * 关键事件：数据库首次创建 / 版本升级时触发
     * 只有这个事件内可以修改数据库结构（创建仓库、索引）
     */
    request.onupgradeneeded = (event) => {
      const db = event.target.result;
      console.log("数据库版本升级/首次创建，当前版本：", event.oldVersion);

      // 示例：创建名为 "users" 的对象仓库，主键为 "id"
      if (!db.objectStoreNames.contains("users")) {
        const userStore = db.createObjectStore("users", {
          keyPath: "id", // 主键字段
          autoIncrement: false // 主键是否自增
        });
        console.log("对象仓库 users 创建成功");

        // 为 users 仓库创建索引：name（非唯一）、phone（唯一）
        userStore.createIndex("name", "name", { unique: false });
        userStore.createIndex("phone", "phone", { unique: true });
        console.log("users 仓库索引创建成功");
      }
    };

    // 数据库打开成功
    request.onsuccess = (event) => {
      dbInstance = event.target.result;
      console.log("数据库打开成功");
      resolve(dbInstance);
    };

    // 数据库打开失败
    request.onerror = (event) => {
      reject(new Error(`数据库打开失败：${event.target.error.message}`));
    };

    // 版本升级被阻塞（如其他标签页打开了同数据库）
    request.onblocked = () => {
      reject(new Error("数据库升级被阻塞，请关闭其他标签页后重试"));
    };
  });
}
```

### 2.  关闭数据库
数据库使用完毕后，建议手动关闭，释放资源。
```javascript
/**
 * 关闭数据库
 */
function closeDB() {
  if (dbInstance) {
    dbInstance.close();
    dbInstance = null;
    console.log("数据库已关闭");
  }
}
```

### 3.  删除数据库
删除整个数据库（谨慎操作，会删除所有仓库和数据）。
```javascript
/**
 * 删除数据库
 * @param {string} dbName 数据库名称
 * @returns {Promise<void>}
 */
function deleteDB(dbName) {
  return new Promise((resolve, reject) => {
    const request = indexedDB.deleteDatabase(dbName);
    request.onsuccess = () => {
      dbInstance = null;
      console.log("数据库删除成功");
      resolve();
    };
    request.onerror = (event) => {
      reject(new Error(`数据库删除失败：${event.target.error.message}`));
    };
  });
}
```

## 三、 核心 CRUD 操作
所有操作都需通过**事务**执行，以下是通用的增删改查方法封装。

### 1.  新增数据（add/put）
- `add`: 主键不存在时新增，主键存在则**报错**。
- `put`: 主键不存在时新增，主键存在则**更新**（推荐使用，兼顾新增和更新）。
```javascript
/**
 * 新增/更新数据
 * @param {string} storeName 仓库名
 * @param {object} data 要存储的数据
 * @returns {Promise<any>} 操作结果
 */
function saveData(storeName, data) {
  return new Promise((resolve, reject) => {
    if (!dbInstance) {
      return reject(new Error("数据库未打开"));
    }
    // 开启事务：参数1=仓库名数组，参数2=事务模式
    const tx = dbInstance.transaction([storeName], "readwrite");
    const store = tx.objectStore(storeName);
    // 使用 put 方法
    const request = store.put(data);

    request.onsuccess = (event) => {
      resolve(event.target.result); // 返回主键值
    };
    request.onerror = (event) => {
      reject(new Error(`数据保存失败：${event.target.error.message}`));
    };
  });
}

// 调用示例
// saveData("users", { id: 1, name: "张三", phone: "13800138000" });
```

### 2.  查询数据
#### （1） 根据主键查询单条数据
```javascript
/**
 * 根据主键查询单条数据
 * @param {string} storeName 仓库名
 * @param {any} key 主键值
 * @returns {Promise<any>} 查询结果
 */
function getDataByKey(storeName, key) {
  return new Promise((resolve, reject) => {
    if (!dbInstance) {
      return reject(new Error("数据库未打开"));
    }
    const tx = dbInstance.transaction([storeName], "readonly");
    const store = tx.objectStore(storeName);
    const request = store.get(key);

    request.onsuccess = (event) => {
      resolve(event.target.result); // 返回查询到的数据
    };
    request.onerror = (event) => {
      reject(new Error(`查询失败：${event.target.error.message}`));
    };
  });
}

// 调用示例
// getDataByKey("users", 1).then(user => console.log(user));
```

#### （2） 根据索引查询数据
通过索引字段查询，支持精准匹配和范围匹配。
```javascript
/**
 * 根据索引查询数据
 * @param {string} storeName 仓库名
 * @param {string} indexKey 索引字段名
 * @param {any} indexValue 索引字段值
 * @returns {Promise<Array>} 查询结果数组
 */
function getDataByIndex(storeName, indexKey, indexValue) {
  return new Promise((resolve, reject) => {
    if (!dbInstance) {
      return reject(new Error("数据库未打开"));
    }
    const tx = dbInstance.transaction([storeName], "readonly");
    const store = tx.objectStore(storeName);
    const index = store.index(indexKey); // 获取索引对象
    const result = [];

    // 打开游标遍历索引匹配的数据
    index.openCursor(IDBKeyRange.only(indexValue)).onsuccess = (event) => {
      const cursor = event.target.result;
      if (cursor) {
        result.push(cursor.value);
        cursor.continue(); // 继续遍历下一条
      } else {
        resolve(result); // 遍历完成，返回结果
      }
    };
  });
}

// 调用示例
// getDataByIndex("users", "name", "张三").then(users => console.log(users));
```

#### （3） 查询仓库所有数据
```javascript
/**
 * 查询仓库所有数据
 * @param {string} storeName 仓库名
 * @returns {Promise<Array>} 所有数据数组
 */
function getAllData(storeName) {
  return new Promise((resolve, reject) => {
    if (!dbInstance) {
      return reject(new Error("数据库未打开"));
    }
    const tx = dbInstance.transaction([storeName], "readonly");
    const store = tx.objectStore(storeName);
    const request = store.getAll(); // 直接获取所有数据

    request.onsuccess = (event) => {
      resolve(event.target.result);
    };
    request.onerror = (event) => {
      reject(new Error(`查询所有数据失败：${event.target.error.message}`));
    };
  });
}
```

### 3.  删除数据
#### （1） 根据主键删除单条数据
```javascript
/**
 * 根据主键删除数据
 * @param {string} storeName 仓库名
 * @param {any} key 主键值
 * @returns {Promise<void>}
 */
function deleteDataByKey(storeName, key) {
  return new Promise((resolve, reject) => {
    if (!dbInstance) {
      return reject(new Error("数据库未打开"));
    }
    const tx = dbInstance.transaction([storeName], "readwrite");
    const store = tx.objectStore(storeName);
    const request = store.delete(key);

    request.onsuccess = () => {
      console.log("数据删除成功");
      resolve();
    };
    request.onerror = (event) => {
      reject(new Error(`数据删除失败：${event.target.error.message}`));
    };
  });
}
```

#### （2） 清空仓库所有数据
```javascript
/**
 * 清空仓库所有数据
 * @param {string} storeName 仓库名
 * @returns {Promise<void>}
 */
function clearStore(storeName) {
  return new Promise((resolve, reject) => {
    if (!dbInstance) {
      return reject(new Error("数据库未打开"));
    }
    const tx = dbInstance.transaction([storeName], "readwrite");
    const store = tx.objectStore(storeName);
    const request = store.clear();

    request.onsuccess = () => {
      console.log("仓库数据清空成功");
      resolve();
    };
    request.onerror = (event) => {
      reject(new Error(`清空仓库失败：${event.target.error.message}`));
    };
  });
}
```

## 四、 游标高级用法
游标是处理大量数据、范围查询、分页的核心工具，配合 `IDBKeyRange` 可实现复杂查询。
```javascript
/**
 * 范围查询数据（使用游标+键范围）
 * @param {string} storeName 仓库名
 * @param {string} indexKey 索引字段（可选，不传则按主键查询）
 * @param {object} rangeConfig 范围配置 { lower, upper, lowerOpen, upperOpen }
 * @returns {Promise<Array>}
 */
function queryDataByRange(storeName, indexKey = null, rangeConfig) {
  return new Promise((resolve, reject) => {
    if (!dbInstance) {
      return reject(new Error("数据库未打开"));
    }
    const tx = dbInstance.transaction([storeName], "readonly");
    const store = tx.objectStore(storeName);
    // 选择查询对象：索引 或 仓库本身（按主键）
    const target = indexKey ? store.index(indexKey) : store;
    // 创建键范围
    const keyRange = IDBKeyRange.bound(
      rangeConfig.lower,
      rangeConfig.upper,
      rangeConfig.lowerOpen || false,
      rangeConfig.upperOpen || false
    );
    const result = [];

    // 打开游标，设置遍历方向（默认 next，可选 prev 倒序）
    target.openCursor(keyRange, "next").onsuccess = (event) => {
      const cursor = event.target.result;
      if (cursor) {
        result.push(cursor.value);
        // 分页技巧：cursor.advance(10) 跳过10条数据
        cursor.continue();
      } else {
        resolve(result);
      }
    };
  });
}

// 调用示例：查询 users 仓库中 id 大于 1 且小于 10 的数据
// queryDataByRange("users", null, { lower: 1, upper: 10, lowerOpen: true, upperOpen: true })
//  .then(data => console.log(data));
```

## 五、 版本升级与结构修改
IndexedDB 中**修改数据库结构（增删仓库、索引）必须通过版本升级实现**，步骤如下：
1.  **递增版本号**：调用 `openDB` 时传入比当前版本大的版本号。
2.  **在 `onupgradeneeded` 中修改结构**：该事件是唯一可执行 `createObjectStore`、`deleteObjectStore`、`createIndex`、`deleteIndex` 的地方。

```javascript
// 示例：版本从 1 升级到 2，新增仓库 "goods"，为仓库 "goods"，为 "users" 新增索引 "age"
function upgradeDB() {
  openDB("myDB", 2).then(db => {
    // 升级逻辑在 onupgradeneeded 中执行
  }).catch(err => console.error(err));
}

// 对应的 onupgradeneeded 事件处理补充
// request.onupgradeneeded = (event) => {
//   const db = event.target.result;
//   const oldVersion = event.oldVersion;
//   // 版本从 1 升级到 2
//   if (oldVersion < 2) {
//     // 新增 goods 仓库
//     if (!db.objectStoreNames.contains("goods")) {
//       const goodsStore = db.createObjectStore("goods", { keyPath: "goodsId" });
//       goodsStore.createIndex("price", "price", { unique: false });
//     }
//     // 为 users 仓库新增 age 索引
//     const userStore = event.target.transaction.objectStore("users");
//     if (!userStore.indexNames.contains("age")) {
//       userStore.createIndex("age", "age", { unique: false });
//     }
//   }
// };
```

## 六、 性能优化技巧
1.  **复用数据库实例**：避免频繁打开/关闭数据库，全局缓存 `dbInstance`。
2.  **合理选择事务模式**：纯查询用 `readonly`，读写用 `readwrite`，`readonly` 性能更高且可并行执行。
3.  **批量操作合并事务**：多个增删改操作放在同一个事务中执行，减少事务创建和提交的开销。
4.  **索引驱动查询**：高频查询字段必须创建索引，避免全表扫描；低频查询字段可不建索引节省空间。
5.  **游标分页优化**：使用 `cursor.advance(n)` 实现分页，避免一次性加载大量数据导致页面卡顿。
6.  **大数据操作拆分为小事务**：单次操作大量数据时，拆分为多个小事务分批执行，防止阻塞主线程。
7.  **避免事务内异步操作**：事务会在同步代码执行完毕后自动提交，异步操作（如 `setTimeout`）会导致事务提前结束。

## 七、 常见问题与解决方案
| 问题现象 | 原因分析 | 解决方案 |
|----------|----------|----------|
| `add` 操作报错“主键重复” | `add` 方法不允许主键重复 | 改用 `put` 方法，兼顾新增和更新 |
| 版本升级不触发 `onupgradeneeded` | 版本号未递增或小于当前版本 | 确保新版本号 > 旧版本号，且仅支持递增 |
| 游标遍历无结果，事务提前结束 | 事务内存在异步操作，导致事务提前提交 | 确保游标遍历在同步代码中完成，不嵌套异步逻辑 |
| 跨标签页操作数据库冲突 | 多个标签页同时打开同一数据库并修改结构 | 监听 `versionchange` 事件，冲突时关闭当前数据库实例 |
| 大数据量操作导致页面卡顿 | 主线程被同步操作阻塞 | 使用 **Web Worker** 执行大数据量操作，避免阻塞UI |
| 浏览器提示“磁盘配额不足” | 存储数据量超过浏览器分配的配额 | 清理无用数据，或引导用户手动增加配额（部分浏览器支持） |

## 八、 兼容性与替代方案
1.  **兼容性**：
    - 支持所有现代浏览器（Chrome、Firefox、Safari 10+、Edge），IE 10 及以上部分支持（存在兼容坑）。
    - 移动端浏览器基本支持，可放心用于移动端 H5 应用。

2.  **替代方案**：
    - **localForage**：封装了 IndexedDB、WebSQL、localStorage 的开源库，提供统一 API，自动降级兼容，推荐前端项目使用。
    - **PouchDB**：支持离线存储的开源库，可与 CouchDB 同步，适合复杂离线应用。

## 九、 核心总结
1.  IndexedDB 是**异步、事务型、大容量**的前端本地数据库，适合存储结构化数据。
2.  核心流程：**打开数据库 → 开启事务 → 操作仓库 → 处理结果**。
3.  性能关键：**索引优先、事务合并、游标分页**。
4.  结构修改唯一入口：`onupgradeneeded` 事件，必须通过版本升级实现。
5.  最佳实践：封装通用 CRUD 方法，避免重复代码；区分事务模式，优化查询性能。
内容由 AI 生成