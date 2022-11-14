---
title: 缓存已编译的 WebAssembly 模块
slug: WebAssembly/Caching_modules
---

{{WebAssemblySidebar}}

对于提高应用的性能来说，缓存是很有用的——我们可以在客户端存储已编译的 WebAssembly 模块，从而可以避免每次都下载和编译它们。本文解释了这方面的最佳实践。

## 使用 IndexedDB 实现缓存

[IndexedDB](/zh-CN/docs/Web/API/IndexedDB_API)是一个事务型数据库系统，它允许你在客户端存储和获取结构化数据。它适合在本地保存包含应用程序状态的资源，包括文本、二进制大对象以及其他任何可以克隆的对象。

这包括已编译的 wasm 模块（{{jsxref("WebAssembly.Module")}} JavaScript 对象）。

## 建立缓存库

因为 IndexedDB 在一定程度上是老式风格的 API，所以，我们想提供一个库函数以便加快写缓存代码的速度并使它能够更好的与当今更现代的 API 配合。

在我们的[wasm-utils.js](https://github.com/mdn/webassembly-examples/blob/gh-pages/wasm-utils.js)脚本库中，你会发现 instantiateCachedURL()——该函数使用给定版本的 dbVersion 获取给定 url 的 wasm 模块，使用给定的 importObject 实例化，并且返回一个将会解析成最终的 wasm 实例的 promise。而且，它会创建一个用来把已编译的 wasm 模块缓存起来的数据库，尝试在数据库中存储新的模块，并且从数据库中获取之前缓存的模块，从而使你免于再次下载它们。

> **备注：** 整个网站的 wasm 缓存（不只是给定的 URL）是通过传入到函数中的 dbVersion 进行版本控制的。如果 wasm 模块代码更新了或者它的 URL 发生了变化，你需要更新 dbVersion。对于 instantiateCachedURL() 的任何后续调用将会清除掉全部的缓存，从而使你避免使用过时的模块。

该函数从定义一些必要的常量开始：

```js
function instantiateCachedURL(dbVersion, url, importObject) {
  const dbName = 'wasm-cache';
  const storeName = 'wasm-cache';
```

### 建立数据库

在 instantiateCachedURL() 中的第一个辅助函数 openDatabase()，创建一个存储 wasm 模块的对象存储空间，以及在 dbVersion 更新后清除数据库；它返回一个解析成新的数据库的 promise。

```js
  function openDatabase() {
    return new Promise((resolve, reject) => {
      var request = indexedDB.open(dbName, dbVersion);
      request.onerror = reject.bind(null, 'Error opening wasm cache database');
      request.onsuccess = () => { resolve(request.result) };
      request.onupgradeneeded = event => {
        var db = request.result;
        if (db.objectStoreNames.contains(storeName)) {
            console.log(`Clearing out version ${event.oldVersion} wasm cache`);
            db.deleteObjectStore(storeName);
        }
        console.log(`Creating version ${event.newVersion} wasm cache`);
        db.createObjectStore(storeName)
      };
    });
  }
```

### 在数据库中查找模块

我们的下一个函数 lookupInDatabase()，提供了一个简单的基于 promise 的操作，用来在我们之前创建的对象存储空间中查找给定的 url。如果成功，它可以解析出存储的已编译模块；如果失败，它会给出一个错误。

```js
  function lookupInDatabase(db) {
    return new Promise((resolve, reject) => {
      var store = db.transaction([storeName]).objectStore(storeName);
      var request = store.get(url);
      request.onerror = reject.bind(null, `Error getting wasm module ${url}`);
      request.onsuccess = event => {
        if (request.result)
          resolve(request.result);
        else
          reject(`Module ${url} was not found in wasm cache`);
      }
    });
  }
```

### 存储和实例化模块

接下来，我们定义了一个函数 storeInDatabase()，它可以触发一个异步操作，从而在给定的数据库中存储给定的 wasm 模块。

```js
  function storeInDatabase(db, module) {
    var store = db.transaction([storeName], 'readwrite').objectStore(storeName);
    var request = store.put(module, url);
    request.onerror = err => { console.log(`Failed to store in wasm cache: ${err}`) };
    request.onsuccess = err => { console.log(`Successfully stored ${url} in wasm cache`) };
  }
```

最后，我们定义一个辅助函数——fetchAndInstantiate()，它从给定的 url 获取数据，将其编译成一个模块，并且使用给定的导入对象实例化该模块。

```js
  function fetchAndInstantiate() {
    return fetch(url).then(response =>
      response.arrayBuffer()
    ).then(buffer =>
      WebAssembly.instantiate(buffer, importObject)
    )
  }
```

### 使用辅助函数

使用这些定义好的基于 Promise 的辅助函数，我们现在可以表达一个 IndexedDB 缓存查找的核心逻辑了。首先，我们通过尝试打开一个数据库，然后，查看在给定的数据库 db 中是否存在与 url 相对应的模块：

```js
  return openDatabase().then(db => {
    return lookupInDatabase(db).then(module => {
```

如果找到了模块，那么，使用给定的导入对象对其进行实例化：

```js
      console.log(`Found ${url} in wasm cache`);
      return WebAssembly.instantiate(module, importObject);
    },
```

如果没有找到，那么，我们从零开始编译它，然后，使用给定的 url 作为键，将已编译的模块存储到数据库中，方便下次使用。

```js
    errMsg => {
      console.log(errMsg);
      return fetchAndInstantiate().then(results => {
        storeInDatabase(db, results.module);
        return results.instance;
      });
    })
  },
```

> **备注：** [WebAssembly.instantiate()](/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/instantiate)返回一个[模块](/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Module)和[实例](/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Instance)为的就是这种用处：模块表示已经编译的代码，并且可以在 IndexedDB 中存取或者通过[postMessage()](/zh-CN/docs/Web/API/MessagePort/postMessage)在 Workers 之间共享；实例是具有状态的，并且包含了可以调用的 JavaScript 函数，因此不能够被存储或者共享。

如果打开数据库失败（比如由于权限或者空间限制），我们改用获取和编译模块的方式，并且不再尝试保存结果（因为没有数据库可以保存它们）。

```js
  errMsg => {
    console.log(errMsg);
    return fetchAndInstantiate().then(results =>
      results.instance
    );
  });
}
```

## 缓存 wasm 模块

有了上面定义的库函数，取得一个 wasm 模块实例，并且使用它的导出特性（同时在后台处理缓存）只需要使用下面的参数进行调用即可：

- 缓存的版本号——正如我们上面解释的那样，当任何 wasm 模块发生更新或者移动到不同的 URL，你都需要更新它。
- 你想要实例化的 wasm 模块的 URL。
- 一个可选的导入对象。

```js
const wasmCacheVersion = 1;

instantiateCachedURL(wasmCacheVersion, 'test.wasm').then(instance =>
  console.log("Instance says the answer is: " + instance.exports.answer())
).catch(err =>
  console.error("Failure to instantiate: " + err)
);
```

你可以在 GitHub 上找到这个例子的源代码 [indexeddb-cache.html](https://github.com/mdn/webassembly-examples/blob/gh-pages/other-examples/indexeddb-cache.html) (或者[实时运行](https://mdn.github.io/webassembly-examples/other-examples/indexeddb-cache.html))。