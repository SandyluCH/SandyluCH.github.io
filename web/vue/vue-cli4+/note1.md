## vue-cli4使用实践


### @vue/cli-service
使用vue-cli-service打包的时候， 如果没有配置builtInolugins和inlinePlugins， 项目的plugins取决于package.json中的devDependencies 和 dependencies中的插件（满足格式 ````/^(@vue\/|vue-|@[\w-]+(\.)?[\w-]+\/vue-)cli-plugin-/````的插件）, 所以打包的时候，要注意不需要的插件及时处理掉。