### react ssr
react-dom 提供了server的渲染api renderToString, 该api将react组件解析成html，
需要注意， node不支持jsx， 需要babel支持， 以及针对服务端渲染代码，我们可以剔除node_modules, 从而大幅减少服务端代码生成耗时。
同构应用需要两个入口，分别编译client和server的代码。