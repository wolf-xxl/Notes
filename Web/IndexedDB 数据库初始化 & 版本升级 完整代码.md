
基于原生 IndexedDB API 编写，兼容主流浏览器，支持**仓库创建、索引配置、版本迁移**，与之前设计的数据库结构完全匹配。

## 一、 核心常量定义
```javascript
// 数据库配置
const DB_CONFIG = {
  name: "TarkovAssistantDB",
  version: 1, // 结构变更时递增
  stores: [
    { name: "keys", keyPath: "id" },
    { name: "tasks", keyPath: "id" },
    { name: "bullets", keyPath: "id" },
    { name: "items", keyPath: "id" },
    { name: "hiddens", keyPath: "id" },
    { name: "userStates", keyPath: "id" }
  ]
};

// 索引配置：[仓库名, 索引字段, 索引选项]
const INDEX_CONFIGS = [
  // keys 仓库索引
  ["keys", "name", { unique: true }],
  ["keys", "mapName", { unique: false }],
  ["keys", "recommend", { unique: false }],
  ["keys", "taskNeed", { unique: false }],
  ["keys", "version", { unique: false }],

  // tasks 仓库索引
  ["tasks", "name", { unique: true }],
  ["tasks", "maps.name", { unique: false, multiEntry: true }],
  ["tasks", "tags", { unique: false, multiEntry: true }],
  ["tasks", "trader", { unique: false }],
  ["tasks", "version", { unique: false }],

  // bullets 仓库索引
  ["bullets", "name", { unique: true }],
  ["bullets", "caliber", { unique: false }],
  ["bullets", "fleshDmg", { unique: false }],
  ["bullets", "armorPen", { unique: false }],
  ["bullets", "version", { unique: false }],

  // items 仓库索引
  ["items", "name", { unique: true }],
  ["items", "category", { unique: false }],
  ["items", "value", { unique: false }],
  ["items", "valuePerVolume", { unique: false }],
  ["items", "version", { unique: false }],

  // hiddens 仓库索引
  ["hiddens", "name", { unique: true }],
  ["hiddens", "version", { unique: false }]
];
```

## 二、 数据库初始化核心方法
```javascript
let dbInstance = null; // 数据库实例缓存

/**
 * 打开/初始化数据库
 * @returns {Promise<IDBDatabase>} 数据库实例
 */
function openDatabase() {
  return new Promise((resolve, reject) => {
    // 关闭已有实例，避免版本冲突
    if (dbInstance) {
      dbInstance.close();
    }

    const request = indexedDB.open(DB_CONFIG.name, DB_CONFIG.version);

    // 数据库首次创建 / 版本升级时触发
    request.onupgradeneeded = (event) => {
      const db = event.target.result;
      const tx = event.target.transaction;

      // 1. 创建对象仓库
      DB_CONFIG.stores.forEach(({ name, keyPath }) => {
        if (!db.objectStoreNames.contains(name)) {
          const store = db.createObjectStore(name, { keyPath, autoIncrement: false });
          console.log(`仓库 ${name} 创建成功`);
        }
      });

      // 2. 创建索引
      INDEX_CONFIGS.forEach(([storeName, indexKey, options]) => {
        const store = tx.objectStore(storeName);
        if (!store.indexNames.contains(indexKey)) {
          store.createIndex(indexKey, indexKey, options);
          console.log(`仓库 ${storeName} 索引 ${indexKey} 创建成功`);
        }
      });

      // 3. 初始化 userStates 仓库默认数据
      const userStore = tx.objectStore("userStates");
      userStore.get("user_state_001").onsuccess = (e) => {
        if (!e.target.result) {
          userStore.add({
            id: "user_state_001",
            taskCompleted: {},
            hiddenCurrentLevels: {},
            lastUpdateTime: {},
            searchHistory: []
          });
          console.log("用户状态默认数据初始化成功");
        }
      };
    };

    // 打开成功
    request.onsuccess = (event) => {
      dbInstance = event.target.result;
      console.log("数据库打开成功");
      resolve(dbInstance);
    };

    // 打开失败
    request.onerror = (event) => {
      reject(new Error(`数据库打开失败: ${event.target.error.message}`));
    };

    // 版本升级失败
    request.onblocked = () => {
      reject(new Error("数据库版本升级被阻塞，请关闭其他标签页后重试"));
    };
  });
}

/**
 * 关闭数据库
 */
function closeDatabase() {
  if (dbInstance) {
    dbInstance.close();
    dbInstance = null;
    console.log("数据库已关闭");
  }
}
```

## 三、 版本升级处理逻辑
当数据库结构需要变更（如新增字段、索引）时，**修改 `DB_CONFIG.version` 并新增对应配置**，示例如下：
```javascript
// 示例：版本升级到 2，给 items 仓库新增索引 "volume"
// 1. 修改版本号
// DB_CONFIG.version = 2;

// 2. 新增索引配置
// INDEX_CONFIGS.push(["items", "volume", { unique: false }]);

// 3. 重新执行 openDatabase() 自动触发升级
```

升级时 `onupgradeneeded` 会自动执行，**不会删除原有数据**，仅执行新增的仓库/索引创建逻辑。

## 四、 通用 CURD 工具方法
基于初始化的实例，封装常用操作，简化业务代码调用：
```javascript
/**
 * 通用数据操作方法
 * @param {string} storeName 仓库名
 * @param {string} type 操作类型: add / put / get / delete / clear
 * @param {any} data 操作数据
 * @returns {Promise<any>} 操作结果
 */
function operateDB(storeName, type, data = null) {
  return new Promise((resolve, reject) => {
    if (!dbInstance) {
      return reject(new Error("数据库未打开，请先调用 openDatabase"));
    }

    const tx = dbInstance.transaction(storeName, "readwrite");
    const store = tx.objectStore(storeName);
    let request;

    switch (type) {
      case "add":
        request = store.add(data);
        break;
      case "put":
        request = store.put(data);
        break;
      case "get":
        request = store.get(data);
        break;
      case "delete":
        request = store.delete(data);
        break;
      case "clear":
        request = store.clear();
        break;
      default:
        return reject(new Error("不支持的操作类型"));
    }

    request.onsuccess = (e) => resolve(e.target.result);
    request.onerror = (e) => reject(e.target.error);
    tx.oncomplete = () => console.log(`[${storeName}] ${type} 操作完成`);
  });
}

// 示例调用
// 1. 打开数据库
// openDatabase().then(() => {
//   // 2. 添加一条物品数据
//   operateDB("items", "add", {
//     id: "item_001",
//     name: "螺栓",
//     category: "耗材",
//     volume: 1,
//     value: 100,
//     valuePerVolume: 100,
//     version: 1
//   });
// });
```

## 五、 使用注意事项
1.  **首次加载**：页面初始化时调用 `openDatabase()`，建议放在 `DOMContentLoaded` 事件中。
2.  **版本管理**：每次结构变更必须递增 `version`，否则 `onupgradeneeded` 不会触发。
3.  **事务生命周期**：所有操作必须在事务内完成，避免长时间占用事务导致阻塞。
4.  **兼容性**：低版本浏览器需引入 `localForage` 作为垫片，API 可无缝兼容。

我可以再帮你编写**基于这个数据库的需求计算核心函数**，直接对接前端悬浮展示逻辑，需要吗？
用户:
需要
内容由 AI 生成