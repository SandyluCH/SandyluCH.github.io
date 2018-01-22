#AggressiveSplittingPlugin
  可将bundles 分成更小的chunks,分解每个chunk直到它的大小达到了配置中设置的maxSize.
  
  例如：当我们require  react等这种大模块的时候，我们可采用这种配置
  webpack.config.js
  module.export = {
    plugins:[new webpack.optimize.AggressiveSplittingPlugin({
			minSize: 30000,
			maxSize: 50000
		})],
    recordsOutputPath:path.join(__dirname, "js", "records.json")
  
  }
  