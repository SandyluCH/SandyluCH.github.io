## Wrap

一个小部件在多个横向或者纵向展示它的孩子。

一个wrap布局它的孩子， 试图在给定方向的main axis上面 挨着上一个child排放这个child,并在孩子之间留距离。 如果这里面没有足够的空间排放child, wrap会在cross axis上创建一个新的run 临近已存在的child.

children的排列规则 取决于main axis的alignment, cross axis的crossAxisAlignment.

单行的wrap与Row表现几乎一致， 单行的wrap跟column表现几乎一致。但Row与Column都是单行单列的，wrap则突破了这个限制。mainAxis上空间不足时，则向crossAxis上去扩展。 从效率上，flow布局比wrap布局效率高。

## flow



