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

## 参考文档:[How a Bitcoin Transaction Works](https://www.ccn.com/bitcoin-transaction-really-works/)

Bitcoin transaction的目的是转让Bitcoin的数量的所有权 到 Bitcoin的地址上。当你发送Bitcoin的时候， 一个简单的数据结构叫Bitcoin transaction会被你的钱包客户端创建，然后在网络中广播。在网络中的Bitcoin节点将会转播和重新广播这个transaction, 如果这个transaction是有效的,将会被包含的在他们正在挖矿的block的节点中,10到20分钟内,这个transaction和其他的transaction一起被包含在blockchain的block中,这时,接收者才能看到在他们的钱包中看到这个transaction的数量.
例子:
![avatar](https://248qms3nhmvl15d4ne1i4pxl-wpengine.netdna-ssl.com/wp-content/uploads/2014/07/Bitcoin_tx_example.png)
The main components of this standard transaction are color-coded:
- Transaction ID (highlighted in yellow)
- Descriptors and meta-data (blue curly brace elaborated upon to the right)
- Inputs (pink area)
- Outputs (green area)

关于transactions有四条不言自明的真相:
- 任何Bitcoin的数量总是被发送到地址上
- 我们收到的任何Bitcoin的数量是被locked到receiving地址(通常是与我们的钱包相关的)
- 我们花费在Bitcoin上的时间,我们花费的数量总是来自之前收到的和当前钱包的资金
- 地址接收Bitcoin,但是他们不发送Bitcoin,Bitcoin是由钱包来发送

进入钱包的数量不像物理钱包中的coin。接收到的数量并不混合,但是仍然保持分开并且不同于钱包接收到的实际数量。

例子:
假如你创建一个银行钱包,他收到了3个数量:0.01 、0.2、3，然后你发送3BTC给钱包相关的地址，两个两笔支付由Alice发起。
![avatar](https://248qms3nhmvl15d4ne1i4pxl-wpengine.netdna-ssl.com/wp-content/uploads/2014/07/Bitcoin_Transactions_Alice.png)
在钱包中,这三个数不是合在一起的,它们是独自分开的,放在一起由原始的transaction:0.01 , 0.2 和3 .
![avatar](https://248qms3nhmvl15d4ne1i4pxl-wpengine.netdna-ssl.com/wp-content/uploads/2014/07/Bitcoin_Transactions_wallet.png)

例子:
Let’s consider an example by following the money in a scenario where you send 0.15 BTC to Bob.

As we have seen, your wallet does not select 15 mil satoshi (0.15 BTC) from an undifferentiated pool of 321 mil satoshi making up the wallet balance. Instead, the wallet selects a spend candidate from amongst the three existing “outputs” contained in the wallet. So, it chooses (for various reasons that are not important now) the 0.2 BTC output. The wallet will unlock the 0.2 BTC output and use the whole amount of 0.2 BTC as an input to your new 0.15 BTC transaction. The 0.2 BTC output is “spent” in the process. –Read this paragraph a second time.
![avatar](https://248qms3nhmvl15d4ne1i4pxl-wpengine.netdna-ssl.com/wp-content/uploads/2014/07/Bitcoin_Transactions_Bob.png)

The spend transaction your wallet creates will send 0.15 BTC to Bob’s address – where it will reside in his wallet as an output – waiting eventually to be spent.

The 0.05 BTC difference (0.2 BTC input minus 0.15 BTC output) is called “change” and the transaction will send this back to your wallet via a newly created address. The 0.05 BTC change amount will reside in your wallet as a new output – waiting eventually to be spent. So, now, a virtual peek inside your wallet reveals the following:
![avatar](https://248qms3nhmvl15d4ne1i4pxl-wpengine.netdna-ssl.com/wp-content/uploads/2014/07/Bitcoin_Transactions_summary.png)
Each of the three outputs that are “waiting to be spent”, is locked to its receiving addresses until such time as one or more of them are selected as input(s) to a new spend transaction.

Behind the scenes, different wallet clients apply different logic rules when selecting UTXOs as inputs to new transactions. A sane wallet policy is to use older UTXOs first, wherever possible, but implementations differ. The manner in which UTXOs are selected is not of concern to us right now, since the objective has been emphasis of the point that amounts received to our wallets remain separate and distinct.

总结:
Various received amounts don’t mix as they do in a physical wallet. Instead, received amounts (UTXOs) are used individually (or in combination) at the moment we spend Bitcoin. When creating the spend transaction our wallet selects UTXOs (of sufficient value to satisfy the amount we want to send) and typically creates two new outputs: one for the receiver and one for the change we receive back to our wallet. The change becomes a brand new UTXO in our wallet, and the amount we send becomes a UTXO locked to the recipient address – which may or may not be associated with a wallet, e.g. cold storage. The original UTXO used as input to the spend transaction is “spent” and destroyed forever.

This has been an introduction to how outputs (UTXOs) are handled by wallet software. Once a UTXO is selected for expenditure, it requires the private key associated with the address that received it. This private key redeems the UTXO and allows it to become an input in a new spend transaction. The mechanism whereby previous transaction outputs are reused as the inputs to new transactions is central to the Bitcoin protocol’s function – and exactly as per Satoshi’s design.

