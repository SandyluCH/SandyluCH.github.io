Blockchain的基本操作:
- validation of transactions (验证交易)
- Gathering transaction for a block(收集block的交易)
- Broadcasting valid transactions & blocks(广播有效的交易和区块)
- Consensus on next block creation(下一个区块的创建的共识)
- chaining the blocks to form an immutable record(链接blocks,以形成不可更改的记录)

参与者有两个主要角色:
- 通过创建交易来启动价值转移的参与者
- 作为矿机,矿机的操作有:
  - 添加工作和计算来验证transactions
  - 广播交易
  - 竞争要求创建区块的权利
  - 通过验证区块来达成共识
  - 广播最新创建的区块
  - 确认交易

交易验证由所有的旷工独立执行。该过程设计验证超过20个标准。其中有一些标准是：UTXO的引用、UTXO有效、UTXO的output引用是正确的、引用输入量和输出量足够匹配、不广播无效的交易。

所有有效交易都被添加到一个交易池中，矿工从池中选择一组交易来创建一个区块。所有的旷工们竞相解决一个难题，从而来决定谁有权。一旦一个旷工解决了这个难题，就会在网络中进行广播并广播这个区块。然后其他参与者来验证这个区块，然后验证通过之后，参与者们达成共识将这个新的区块添加到链中，这个新区块也被添加到他们区块链本地副本中，因此一组新的交易被记录并确认，共识的算法被称为工作量证明（PoW）. 被旷工创建的区块的索引如果是0,那么这个区块有一个特殊的UTXO,没有任何输入UTXO,被称为coinbase交易。总而言之，区块链中的主要操作是交易验证，通过参与者共识达成的区块创建。




