## [immutable-js官方文档](https://github.com/immutable-js/immutable-js)

不可变的数据一经创建就无法更改， 从而使应用程序开发更加简单， 没有防御性的复制，并且可以使用简单的逻辑实现高级的记忆和更改检测技术。immutable数据提供了一个变异的API, 它不会就地更新数据， 而是总是生成新的更新数据。

immutable.js提供了许多持久不见的数据结构， 如：List, Stack, Map, OrderedMap, Set, OrderedSet, Record

这些数据结构在现代Javascript VMs 上是最高效的，通过Clojure和Scala推广的hash-maps尝试和vector尝试使用结构共享， 最大限度地减少了赋值和缓存数据的需要。

immutable.js还提供了lazy Seq, 允许高效地链接map和filter等手机方法， 而无需创建中间表示。