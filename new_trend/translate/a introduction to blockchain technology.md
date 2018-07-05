## [a introduction to blockchain technology](https://bitsonblocks.net/2015/09/09/a-gentle-introduction-to-blockchain-technology/)

人们使用“区块链技术”这个术语，有时他们谈论的是比特币区块链，有时是以太坊区块链，有时是其他虚拟货币或数字令牌，有时候是智能合约。但大多数时候，他们谈论的是分布式账本，即在多台计算机上复制的交易列表，而不是存储在中央服务器上。

通常关于blockchain technology都是关于一种数据存储的主题.具有以下特性的数据存储:
- 包含金融交易
- 实时多个系统被复制
- 存在于点对点网络中
- 使用密码和数字签名来证明身份、辨别真伪,并且有强制读写权限
- 被某些参与者写入
- 被某些参与者阅读,也可以被更广泛的读者阅读,并且有机制使其很难改变历史记录,或者至少可以很容易地检测到有人试图这样做

blockchain 是一系列技术的集合.
![avatar](https://bitsonblocks.files.wordpress.com/2015/09/blockchain_bag2.png)

blockchain与普通数据库的区别:blockchain系统是一个包含普通数据库和一些软件的包。这些软件可以添加新行，验证新航符合预先约定的规则,并通过网络监听和广播新行给他们的对等节点,确保所有对等节点在他们的数据库中有相同的数据。


比特币区块链生态系统实际上是一个相当负责的系统，因为它具有双重目标：任何人都应该能够写入比特币区块链，并且不应该有任何集中的权利或控制。

比特币区块链生态系统就像一个复制数据库网络，每个数据库都包含过去比特币交易的相同列表。网络的重要成员称为验证者或节点，他们传递事务数据（支付）和块数据（添加到分类账）。每个验证器独立检查付款和传递的数据块。有一些规则可以使网络按预期运行。

比特币的目标是分散化，即没有控制点，而且是相对匿名的。并不是所有的区块链生态系统都需要由相同的机制，特别是如果参与者可以被识别和信任的行为。

比特币如何处理某些决策如图所示：
![blockchain_approach](https://bitsonblocks.files.wordpress.com/2015/09/bitcoin_approach.jpg)

公共和私有区块链：
根据您是否允许任何人写入您的区块链或已知的经过审核的参与者，您所需要的技术有很大差异。比特币在理论上允许任何人写入账本（但在实践中，实际上只有大约20人一组）。

- Public blockchains 
分类账在两种情况下是公共的:
  - 没有任何其他人授权的可以写数据
  - 没有任何其他人授权的可以读数据
public blockchains,我们通常是指任何人可以写数据。

- private blockchain
反过来,private blockchain就是需要参与者被信任和被知悉.

区块链本身是就是一种数据结构。

Your computer will act as a full node which means:
  - Connecting to the bitcoin network
  - Downloading the blockchain
  - Storing the blockchain
  - Listening for transactions
  - Validating transactions
  - Passing on valid transactions
  - Listening for blocks
  - Validating blocks
  - Passing on valid blocks
  - Creating blocks
  - ‘Mining’ the blocks
  
  
## [the pros and cons of internal blockchains](https://bitsonblocks.net/2015/12/01/the-pros-and-cons-of-internal-blockchains/)