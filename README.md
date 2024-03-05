### PackagePulse-v2 遍历与可视化你的 JavaScript 依赖关系

<h4>> 遍历 node_modules 文件夹并获取依赖信息</h4>

控制台指令：`npm run analyze`<br/>

该脚本旨在分析项目中的依赖关系，并将结果保存为一个 JSON 文件。它遍历项目中的 `node_modules` 目录，查找每个模块的 `package.json` 文件，并提取其中的依赖信息。

- **获取依赖列表：** 使用 `fs` 模块读取 `node_modules` 目录下的所有模块，并递归地查找每个模块的 `package.json` 文件，以获取依赖信息。
- **清理版本号：** 从依赖关系中清除版本号中的特殊符号，如 `~`、`^`、`>`、`<`、`=`，以便更好地比较和处理依赖关系。
- **生成结果：** 对于每个模块，提取其名称、清理后的依赖关系、清理后的开发依赖关系、版本号以及依赖数量，并将其组合成一个对象。
- **写入文件：** 将结果写入名为 `packagesLists.json` 文件中。
- **计时结束：** 在分析完成后，输出遍历所花费的时间。

---

<h4>> 检验是否存在循环依赖和多版本的依赖</h4>

控制台指令：`npm run calculate`<br/>

**循环依赖：** 循环依赖是指在软件项目中，两个或多个模块之间存在互相依赖的情况，导致形成一个闭环。这种情况下，当系统试图加载或执行这些模块时，会出现无限循环或死锁等问题，导致系统无法正常运行<br/>

- **算法原理：** 使用深度优先搜索 (DFS) 算法来检测循环依赖。首先构建依赖关系图，然后从每个模块开始进行 DFS，如果在搜索过程中发现某个依赖已经在当前路径上出现过，则说明存在循环依赖。
- **存储方式：** 将循环依赖的链路存储为字符串数组，每个字符串表示一条循环依赖的路径。

**重复依赖：** 重复依赖是指在软件项目中，同一个依赖项被引入了多次，且不同的引入版本可能存在差异，从而导致项目中存在多个版本的同一依赖项。这种情况下，可能会出现依赖版本冲突、代码冗余等问题，影响项目的可维护性和稳定性。

- **算法原理：** 遍历所有模块的依赖关系，将每个依赖的版本号存储到一个 Map 中，其中键为依赖名称，值为该依赖的所有版本号的集合。如果某个依赖的版本号集合大小大于 1，则表示存在重复依赖。
- **存储方式：** 将重复依赖的信息存储为对象数组，每个对象包含依赖名称和其对应的多个版本号。

---

<h4>> 打开页面查看和检索依赖信息</h4>

控制台指令：`npm run start`<br/>

TreeMap 组件是一个基于 React 和 D3.js 实现的可视化工具，用于在 TreeMap 布局中探索包依赖关系。具有以下特性：

- **分页懒加载渲染**：采用分页懒加载技术，将依赖包数据分页加载到页面中，以优化页面性能和加载速度。
- **交互式可视化**：用户可以通过与页面交互（如滚动浏览器窗口和搜索依赖名称）来浏览包的依赖关系，使得依赖关系的探索更加直观和交互性。

#### 已完成的主要方法

1. **绘制包格子函数 (`drawPKG`)**：根据传入的数据，绘制初始的 TreeMap 布局，以可视化展示包的依赖关系。
2. **绘制依赖格子函数 (`drawDEP`)**：根据选定包的依赖关系，绘制并突出显示依赖格子，以区分不同类型的依赖。
3. **搜索监听**：TreeMap 组件会监听传入的 `filteredData` 属性的变化，当数据变化时重新绘制可见包的依赖关系，实现动态更新。
4. **滚动监听**：TreeMap 组件会监听用户在页面中的滚动事件，一旦检测到用户滚动，会触发分页懒加载渲染新的可见包，提升用户的浏览体验。

![查看当前项目的依赖信息](./img/渲染.gif '查看当前项目的依赖信息')

---

<h4>> 前端工程化Webpack配置</h4>

- **模式设置：** 开发模式 `development` 以启用相应的优化和工具
- **入口和输出设置：** 指定了应用程序的入口文件路径和打包后文件的输出路径、文件名与公共路径
- **开发工具：** 使用了 `eval-cheap-module-source-map` 以提供更快的构建速度和较小的输出文件大小
- **加载器规则：** 针对 `.ts` 和 `.tsx` 文件使用 `babel-loader` 进行转译，并设置了 React 和 TypeScript 的预设
- **样式加载器规则：** 针对 `.css` 文件使用了 `style-loader` 和 `css-loader` 进行处理。
- **解析设置：** 指定了 Webpack 解析文件时应该查找的扩展名。
- **别名设置:** 通过别名设置将 '@' 指定为 src 目录的路径别名。
- **HtmlWebPackPlugin：** 用于生成 HTML 文件，并将打包后的 JS 文件注入到 HTML 文件中。
- **ReactRefreshWebpackPlugin：** 用于支持 React 的热重载功能。

#### 开发服务器设置

- **端口设置：** 将开发服务器的端口设置为 3000。
- **热重载：** 启用了开发服务器的热模块替换 `Hot Module Replacement` 功能。
- **自动打开：** 设置开发服务器在启动时自动打开浏览器。
- **历史 API 回退：** 开启了 `HTML5 History API` 的回退，用于处理单页应用的路由问题。
- **静态文件目录：** 将 public 目录设置为静态文件目录，用于存放不需要经过 Webpack 处理的静态文件。
