# 一、IndexedDB 基础认知

## 1.1 什么是 IndexedDB

IndexedDB 是浏览器提供的一种**本地持久化存储方案**，用于在客户端存储大量结构化数据（如JSON格式数据）。它是HTML5标准的重要组成部分，弥补了localStorage存储容量小（通常5MB）、仅能存储字符串的不足，最大存储容量一般为浏览器分配给网站的磁盘空间上限（通常几十MB到GB级，不同浏览器略有差异）。

IndexedDB 本质是一个**事务型数据库**，支持索引查询，适合存储需要本地持久化、高频访问的大量数据，常见应用场景包括：离线应用数据缓存、复杂表单本地暂存、大数据量本地检索（如本地通讯录、历史记录）等。

## 1.2 核心特性

- **持久化存储**：数据永久保存（除非用户手动清除或代码删除），不受页面刷新、浏览器重启影响；

- **大容量存储**：存储容量远大于localStorage（具体取决于浏览器和设备，一般为50MB~数GB）；

- **非关系型数据库**：基于键值对存储，数据以对象形式存储，无需预先定义固定表结构；

- **支持索引**：可通过索引快速查询数据，提升检索效率；

- **异步操作**：所有核心操作（打开、增删改查）均为异步，避免阻塞主线程，不影响页面渲染；

- **同源限制**：遵循同源策略，不同域名无法访问彼此的IndexedDB数据库；

- **支持事务**：操作以事务为单位，要么全部成功，要么全部失败，保证数据一致性。

## 1.3 与其他本地存储方案对比

|存储方案|存储容量|数据类型|操作方式|适用场景|
|---|---|---|---|---|
|localStorage|约5MB|仅字符串|同步|少量简单数据（如用户偏好设置）|
|sessionStorage|约5MB|仅字符串|同步|会话级数据（页面关闭后丢失）|
|Cookie|约4KB|仅字符串|同步|用户身份标识（随请求发送）|
|IndexedDB|50MB~数GB|任意对象（支持结构化数据）|异步|大量结构化数据、离线应用、本地检索|
## 1.4 核心概念

- **数据库（Database）**：IndexedDB的顶层容器，每个数据库有唯一名称，同一域名下可创建多个数据库；

- **对象仓库（Object Store）**：类似关系型数据库的“表”，用于存储同一类结构化数据，每个数据库可包含多个对象仓库；

- **键（Key）**：唯一标识对象仓库中的数据（类似主键），可自动生成（自增键）或手动指定；

- **索引（Index）**：基于对象的某个属性创建的检索结构，用于快速查询非键属性的数据（类似关系型数据库的索引）；

- **事务（Transaction）**：所有操作必须在事务中执行，支持三种模式：`readwrite`（读写）、`readonly`（只读，默认）、`versionchange`（版本变更，用于创建/删除对象仓库、索引）；

- **游标（Cursor）**：用于遍历对象仓库中的数据，支持按条件筛选、排序，适合批量处理数据。

# 二、IndexedDB 环境准备与兼容性

## 2.1 兼容性

IndexedDB 支持所有现代浏览器（Chrome、Firefox、Safari、Edge），不支持IE9及以下版本。实际开发中可通过以下代码检测浏览器是否支持：

```javascript

// 检测IndexedDB支持性
if (!window.indexedDB) {
  console.error("当前浏览器不支持IndexedDB，无法使用本地存储功能");
} else {
  console.log("当前浏览器支持IndexedDB");
}

```

## 2.2 开发工具

可通过浏览器开发者工具调试IndexedDB，常用工具位置：

- Chrome/Firefox/Edge：F12 → Application（应用）→ IndexedDB；

- Safari：偏好设置 → 高级 → 勾选“在菜单栏中显示开发” → 开发 → 存储 → IndexedDB。

通过开发工具可查看数据库、对象仓库、数据内容，也可手动删除数据、刷新数据库，方便调试。

# 三、IndexedDB 核心操作流程（实战）

IndexedDB 操作遵循固定流程：**打开数据库 → 操作数据（增删改查）→ 关闭数据库**。以下通过完整代码示例，覆盖所有核心操作。

## 3.1 打开/创建数据库

打开数据库时，若数据库不存在则自动创建；若指定的版本号高于当前版本，会触发`versionchange`事务，可在此事务中创建/删除对象仓库、索引。

```javascript

// 数据库配置
const DB_CONFIG = {
  name: "UserDB", // 数据库名称
  version: 1, // 数据库版本（必须为正整数）
  storeName: "UserStore" // 对象仓库名称
};

// 打开/创建数据库
function openDB() {
  return new Promise((resolve, reject) => {
    // 调用indexedDB.open()打开数据库
    const request = window.indexedDB.open(DB_CONFIG.name, DB_CONFIG.version);

    // 数据库打开成功
    request.onsuccess = (event) => {
      const db = event.target.result;
      console.log("数据库打开成功", db);
      resolve(db); // 将数据库实例传递给后续操作
    };

    // 数据库打开失败
    request.onerror = (event) => {
      console.error("数据库打开失败", event.target.error);
      reject(event.target.error);
    };

    // 数据库版本更新（首次创建或版本号提升时触发）
    request.onupgradeneeded = (event) => {
      const db = event.target.result;
      console.log("数据库版本更新，当前版本：", db.version);

      // 检查对象仓库是否已存在，不存在则创建
      if (!db.objectStoreNames.contains(DB_CONFIG.storeName)) {
        // 创建对象仓库，指定键路径（keyPath）为id（手动指定键）
        // 若需自动生成键，可使用autoIncrement: true，此时无需指定keyPath
        const objectStore = db.createObjectStore(DB_CONFIG.storeName, {
          keyPath: "id", // 键路径：数据对象中的id属性作为唯一键
          // autoIncrement: true // 自动生成键（可选，与keyPath二选一）
        });

        // 为对象仓库创建索引（可选，提升查询效率）
        // 索引名称：usernameIndex，索引字段：username，是否唯一：true（用户名唯一）
        objectStore.createIndex("usernameIndex", "username", { unique: true });
        // 索引名称：ageIndex，索引字段：age，是否唯一：false（年龄可重复）
        objectStore.createIndex("ageIndex", "age", { unique: false });

        console.log("对象仓库及索引创建成功");
      }
    };
  });
}

```

## 3.2 关闭数据库

数据操作完成后，应关闭数据库以释放资源（浏览器关闭时会自动关闭，但手动关闭更规范）。

```javascript

// 关闭数据库
function closeDB(db) {
  db.close();
  console.log("数据库已关闭");
}

```

## 3.3 增删改查操作（CRUD）

所有数据操作必须在事务中执行，需先通过数据库实例获取事务，再通过事务获取对象仓库，最后执行具体操作。

### 3.3.1 新增数据（Create）

```javascript

// 新增单条数据
async function addData(data) {
  try {
    const db = await openDB(); // 打开数据库
    // 创建事务：指定操作的对象仓库，模式为readwrite（读写）
    const transaction = db.transaction(DB_CONFIG.storeName, "readwrite");
    // 获取对象仓库
    const objectStore = transaction.objectStore(DB_CONFIG.storeName);
    // 新增数据（put()方法：若数据已存在则更新，add()方法：若数据已存在则报错）
    const request = objectStore.add(data);

    // 新增成功
    request.onsuccess = () => {
      console.log("数据新增成功，键：", request.result);
    };

    // 新增失败
    request.onerror = (event) => {
      console.error("数据新增失败", event.target.error);
    };

    // 事务完成（无论成功失败）
    transaction.oncomplete = () => {
      console.log("新增事务完成");
      closeDB(db); // 关闭数据库
    };
  } catch (error) {
    console.error("新增数据异常", error);
  }
}

// 示例：新增两条用户数据
addData({ id: 1, username: "zhangsan", age: 20, gender: "男" });
addData({ id: 2, username: "lisi", age: 22, gender: "女" });

```

### 3.3.2 查询数据（Read）

IndexedDB 支持多种查询方式：通过键查询、通过索引查询、通过游标遍历，适用于不同场景。

```javascript

// 1. 通过键查询单条数据
async function getDataByKey(key) {
  try {
    const db = await openDB();
    // 创建只读事务（默认模式，可省略第二个参数）
    const transaction = db.transaction(DB_CONFIG.storeName);
    const objectStore = transaction.objectStore(DB_CONFIG.storeName);
    // 通过键获取数据
    const request = objectStore.get(key);

    request.onsuccess = () => {
      if (request.result) {
        console.log("通过键查询到的数据：", request.result);
      } else {
        console.log("未查询到对应数据");
      }
    };

    request.onerror = (event) => {
      console.error("查询失败", event.target.error);
    };

    transaction.oncomplete = () => {
      closeDB(db);
    };
  } catch (error) {
    console.error("查询数据异常", error);
  }
}

// 示例：查询键为1的数据
getDataByKey(1);

// 2. 通过索引查询数据（按用户名查询）
async function getDataByIndex(indexName, indexValue) {
  try {
    const db = await openDB();
    const transaction = db.transaction(DB_CONFIG.storeName);
    const objectStore = transaction.objectStore(DB_CONFIG.storeName);
    // 获取索引
    const index = objectStore.index(indexName);
    // 通过索引值查询数据
    const request = index.get(indexValue);

    request.onsuccess = () => {
      if (request.result) {
        console.log(`通过索引${indexName}查询到的数据：`, request.result);
      } else {
        console.log("未查询到对应数据");
      }
    };

    request.onerror = (event) => {
      console.error("索引查询失败", event.target.error);
    };

    transaction.oncomplete = () => {
      closeDB(db);
    };
  } catch (error) {
    console.error("索引查询异常", error);
  }
}

// 示例：通过usernameIndex查询用户名为lisi的数据
getDataByIndex("usernameIndex", "lisi");

// 3. 通过游标遍历所有数据（适合批量处理）
async function getAllDataByCursor() {
  try {
    const db = await openDB();
    const transaction = db.transaction(DB_CONFIG.storeName);
    const objectStore = transaction.objectStore(DB_CONFIG.storeName);
    // 打开游标（默认按键升序排列）
    const request = objectStore.openCursor();

    // 游标遍历过程
    request.onsuccess = (event) => {
      const cursor = event.target.result;
      if (cursor) {
        // 游标存在，获取当前数据
        console.log("游标遍历到的数据：", cursor.value);
        // 移动到下一条数据
        cursor.continue();
      } else {
        console.log("游标遍历完成，无更多数据");
      }
    };

    request.onerror = (event) => {
      console.error("游标遍历失败", event.target.error);
    };

    transaction.oncomplete = () => {
      closeDB(db);
    };
  } catch (error) {
    console.error("游标遍历异常", error);
  }
}

// 示例：遍历所有用户数据
getAllDataByCursor();

// 4. 查询所有数据（简化版，适合少量数据）
async function getAllData() {
  try {
    const db = await openDB();
    const transaction = db.transaction(DB_CONFIG.storeName);
    const objectStore = transaction.objectStore(DB_CONFIG.storeName);
    // 获取所有数据（适用于数据量较小的场景）
    const request = objectStore.getAll();

    request.onsuccess = () => {
      console.log("所有数据：", request.result);
    };

    request.onerror = (event) => {
      console.error("查询所有数据失败", event.target.error);
    };

    transaction.oncomplete = () => {
      closeDB(db);
    };
  } catch (error) {
    console.error("查询所有数据异常", error);
  }
}

// 示例：查询所有用户数据
getAllData();

```

### 3.3.3 更新数据（Update）

使用`put()`方法更新数据，若数据不存在则新增（与`add()`的区别：`add()`不允许重复键，`put()`允许覆盖重复键的数据）。

```javascript

// 更新数据
async function updateData(data) {
  try {
    const db = await openDB();
    const transaction = db.transaction(DB_CONFIG.storeName, "readwrite");
    const objectStore = transaction.objectStore(DB_CONFIG.storeName);
    // 更新数据（put()：存在则更新，不存在则新增）
    const request = objectStore.put(data);

    request.onsuccess = () => {
      console.log("数据更新成功");
    };

    request.onerror = (event) => {
      console.error("数据更新失败", event.target.error);
    };

    transaction.oncomplete = () => {
      closeDB(db);
    };
  } catch (error) {
    console.error("更新数据异常", error);
  }
}

// 示例：更新id为1的用户年龄为21
updateData({ id: 1, username: "zhangsan", age: 21, gender: "男" });

```

### 3.3.4 删除数据（Delete）

```javascript

// 删除数据（通过键删除）
async function deleteData(key) {
  try {
    const db = await openDB();
    const transaction = db.transaction(DB_CONFIG.storeName, "readwrite");
    const objectStore = transaction.objectStore(DB_CONFIG.storeName);
    // 通过键删除数据
    const request = objectStore.delete(key);

    request.onsuccess = () => {
      console.log("数据删除成功");
    };

    request.onerror = (event) => {
      console.error("数据删除失败", event.target.error);
    };

    transaction.oncomplete = () => {
      closeDB(db);
    };
  } catch (error) {
    console.error("删除数据异常", error);
  }
}

// 示例：删除id为2的用户数据
deleteData(2);

```

## 3.4 高级操作：索引的复杂查询与游标筛选

### 3.4.1 索引范围查询（按年龄范围查询）

```javascript

// 按索引范围查询（查询年龄20~30的用户）
async function getDataByIndexRange(indexName, lowerBound, upperBound) {
  try {
    const db = await openDB();
    const transaction = db.transaction(DB_CONFIG.storeName);
    const objectStore = transaction.objectStore(DB_CONFIG.storeName);
    const index = objectStore.index(indexName);

    // 打开索引游标，指定范围：lowerBound（包含）~ upperBound（不包含）
    // 第三个参数：true表示降序（默认false升序）
    const request = index.openCursor(IDBKeyRange.bound(lowerBound, upperBound));

    request.onsuccess = (event) => {
      const cursor = event.target.result;
      if (cursor) {
        console.log(`年龄${cursor.value.age}的用户：`, cursor.value);
        cursor.continue();
      } else {
        console.log("范围查询完成");
      }
    };

    request.onerror = (event) => {
      console.error("范围查询失败", event.target.error);
    };

    transaction.oncomplete = () => {
      closeDB(db);
    };
  } catch (error) {
    console.error("范围查询异常", error);
  }
}

// 示例：查询年龄20~30的用户
getDataByIndexRange("ageIndex", 20, 30);

```

### 3.4.2 游标筛选与排序

```javascript

// 游标筛选（查询性别为男的用户）并降序排列
async function getFilteredDataByCursor(filterFn, descending = false) {
  try {
    const db = await openDB();
    const transaction = db.transaction(DB_CONFIG.storeName);
    const objectStore = transaction.objectStore(DB_CONFIG.storeName);
    // 打开游标，指定排序方式（descending：true降序，false升序）
    const request = objectStore.openCursor(null, descending ? "prev" : "next");

    request.onsuccess = (event) => {
      const cursor = event.target.result;
      if (cursor) {
        // 应用筛选函数
        if (filterFn(cursor.value)) {
          console.log("筛选后的数据：", cursor.value);
        }
        cursor.continue();
      } else {
        console.log("筛选遍历完成");
      }
    };

    request.onerror = (event) => {
      console.error("筛选遍历失败", event.target.error);
    };

    transaction.oncomplete = () => {
      closeDB(db);
    };
  } catch (error) {
    console.error("筛选遍历异常", error);
  }
}

// 示例：查询性别为男的用户，按降序排列
getFilteredDataByCursor((data) => data.gender === "男", true);

```

## 3.5 删除数据库/对象仓库/索引

删除操作需在`versionchange`事务中执行（即通过提升数据库版本号触发）。

```javascript

// 1. 删除索引
async function deleteIndex(indexName) {
  try {
    // 提升版本号（必须高于当前版本）
    const newVersion = DB_CONFIG.version + 1;
    const db = await new Promise((resolve, reject) => {
      const request = window.indexedDB.open(DB_CONFIG.name, newVersion);
      request.onsuccess = (event) => resolve(event.target.result);
      request.onerror = (event) => reject(event.target.error);
      request.onupgradeneeded = (event) => {
        const db = event.target.result;
        const objectStore = db.transaction(DB_CONFIG.storeName, "versionchange").objectStore(DB_CONFIG.storeName);
        // 删除索引
        if (objectStore.indexNames.contains(indexName)) {
          objectStore.deleteIndex(indexName);
          console.log(`索引${indexName}删除成功`);
        }
      };
    });
    // 更新配置中的版本号
    DB_CONFIG.version = newVersion;
    closeDB(db);
  } catch (error) {
    console.error("删除索引异常", error);
  }
}

// 示例：删除ageIndex索引
deleteIndex("ageIndex");

// 2. 删除对象仓库
async function deleteObjectStore(storeName) {
  try {
    const newVersion = DB_CONFIG.version + 1;
    const db = await new Promise((resolve, reject) => {
      const request = window.indexedDB.open(DB_CONFIG.name, newVersion);
      request.onsuccess = (event) => resolve(event.target.result);
      request.onerror = (event) => reject(event.target.error);
      request.onupgradeneeded = (event) => {
        const db = event.target.result;
        // 删除对象仓库
        if (db.objectStoreNames.contains(storeName)) {
          db.deleteObjectStore(storeName);
          console.log(`对象仓库${storeName}删除成功`);
        }
      };
    });
    DB_CONFIG.version = newVersion;
    closeDB(db);
  } catch (error) {
    console.error("删除对象仓库异常", error);
  }
}

// 3. 删除整个数据库
function deleteDB(dbName) {
  const request = window.indexedDB.deleteDatabase(dbName);
  request.onsuccess = () => {
    console.log(`数据库${dbName}删除成功`);
  };
  request.onerror = (event) => {
    console.error(`数据库${dbName}删除失败`, event.target.error);
  };
}

// 示例：删除UserDB数据库
deleteDB("UserDB");

```

# 四、IndexedDB 事务机制详解

## 4.1 事务的核心特性

IndexedDB 事务遵循**ACID**原则：

- **原子性（Atomicity）**：事务中的所有操作要么全部成功，要么全部失败（若某一步出错，所有操作回滚）；

- **一致性（Consistency）**：事务执行前后，数据库数据保持一致状态；

- **隔离性（Isolation）**：多个事务并发执行时，彼此隔离，不会相互影响；

- **持久性（Durability）**：事务执行成功后，数据修改永久生效，即使浏览器崩溃也不会丢失。

## 4.2 事务的三种模式

|模式|权限|适用场景|备注|
|---|---|---|---|
|readonly|仅查询数据，无法修改|所有查询操作（get、openCursor、getAll等）|默认模式，多个readonly事务可并发执行|
|readwrite|可查询、新增、修改、删除数据|增删改操作（add、put、delete等）|同一对象仓库同一时间仅允许一个readwrite事务执行|
|versionchange|可执行所有操作，包括创建/删除对象仓库、索引、提升版本|数据库结构变更（创建/删除store、index）|仅在indexedDB.open()指定更高版本号时触发，同一时间仅允许一个versionchange事务|
## 4.3 事务的生命周期

1. 通过`db.transaction()`创建事务；

2. 通过事务获取对象仓库或索引，执行具体操作；

3. 事务自动提交：所有操作完成后，事务自动提交（无需手动调用commit()）；

4. 事务回滚：若任意操作失败（如重复键、权限不足），事务自动回滚，所有修改失效；

5. 事务完成：触发`transaction.oncomplete`事件（成功）或`transaction.onerror`事件（失败）。

# 五、IndexedDB 常见问题与解决方案

## 5.1 数据新增失败：重复键错误

**问题现象**：使用`add()`方法新增数据时，报错“ConstraintError: Failed to execute 'add' on 'IDBObjectStore': An object with the same key already exists in the object store”。

**原因**：新增数据的键（keyPath指定的属性）已存在于对象仓库中。

**解决方案**：

- 若需覆盖现有数据，使用`put()`方法替代`add()`；

- 若需确保键唯一，新增前先通过`get()`方法查询键是否存在。

## 5.2 索引查询失败：索引不存在

**问题现象**：获取索引时报错“NotFoundError: Failed to execute 'index' on 'IDBObjectStore': The specified index does not exist”。

**原因**：索引未创建，或创建索引的代码未执行（如数据库版本未提升）。

**解决方案**：

- 确认索引创建代码写在`onupgradeneeded`事件中；

- 提升数据库版本号，触发`onupgradeneeded`事件，执行索引创建逻辑。

## 5.3 异步操作导致的顺序问题

**问题现象**：多个异步操作（如先新增再查询）执行顺序混乱，查询不到刚新增的数据。

**原因**：IndexedDB操作是异步的，若未等待前一个操作的事务完成，直接执行下一个操作，可能导致数据未同步。

**解决方案**：

- 使用Promise+async/await封装操作，确保操作按顺序执行；

- 在事务的`oncomplete`事件中执行后续操作。

## 5.4 存储容量不足

**问题现象**：新增数据时报错“QuotaExceededError: The quota has been exceeded”。

**原因**：超出浏览器分配给IndexedDB的存储容量上限。

**解决方案**：

- 清理无用数据，定期删除过期数据；

- 对于超大文件（如图片、视频），使用File System API替代IndexedDB；

- 在部分浏览器中，可请求用户授权扩大存储容量（需用户手动确认）。

## 5.5 跨域访问限制

**问题现象**：不同域名的页面无法访问彼此的IndexedDB数据库。

**原因**：IndexedDB遵循同源策略，仅允许同一域名（协议、域名、端口均相同）的页面访问。

**解决方案**：

- 若需跨域共享数据，可通过后端接口中转；

- 使用iframe+postMessage实现跨域通信，间接操作数据（需确保两个域名相互信任）。

# 六、IndexedDB 实用工具库推荐

原生IndexedDB API较为繁琐，可使用以下工具库简化开发：

- **localForage**：封装了IndexedDB、localStorage、WebSQL，自动选择最优存储方案，API简洁（类似localStorage），支持Promise；

- **Dexie.js**：轻量级IndexedDB封装库，提供类SQL的查询语法，支持链式调用，开发效率高；

- **idb**：Google开发的极简IndexedDB封装库，体积小，API友好，支持Promise。

示例：使用localForage简化操作（无需关注IndexedDB底层细节）

```javascript

// 安装：npm install localforage
import localforage from "localforage";

// 配置存储方案为IndexedDB
localforage.config({
  driver: localforage.INDEXEDDB,
  name: "UserDB",
  storeName: "UserStore"
});

// 新增数据
localforage.setItem("1", { id: 1, username: "zhangsan", age: 20 })
  .then(() => console.log("新增成功"))
  .catch(err => console.error(err));

// 查询数据
localforage.getItem("1")
  .then(data => console.log("查询到的数据：", data))
  .catch(err => console.error(err));

```

# 七、核心总结

1. IndexedDB 是浏览器端大容量、持久化的结构化存储方案，适合存储需要本地高频访问的大量数据，核心优势是容量大、支持索引、异步非阻塞；

2. 核心操作流程：打开数据库（触发版本更新→创建对象仓库/索引）→ 开启事务→ 操作数据（增删改查）→ 关闭数据库；

3. 所有操作必须在事务中执行，根据需求选择合适的事务模式（readonly/readwrite/versionchange）；

4. 异步操作需用Promise+async/await封装，避免顺序混乱；防重复键、索引创建、容量控制是开发中的关键注意点；

5. 原生API繁琐，小型项目可使用localForage等封装库简化开发，大型项目可考虑Dexie.js提升开发效率。

IndexedDB 是离线应用、PWA（渐进式Web应用）的核心技术之一，掌握其核心用法能大幅提升前端本地数据管理能力，为用户提供更好的离线体验和性能优化。
> （注：文档部分内容可能由 AI 生成）