## bitcoin reference
references:
[Bitcoin whitepage](https://bitcoin.org/bitcoin.pdf)

[Bitcoin's Academic Pedigree](https://queue.acm.org/detail.cfm?id=3136559)

Validation, verification,consensus, Immutable Recording lead to the trust and security of Blockchain.

分类账是有相互不信任的参与者共同维护的全局数据结构。他是不可变，只能添加新的交易，但不能删除、修改或重新排序现有的交易，可随时获取账簿状态的简明加密摘要。

在每个区块中，文件可以用哈希指针的二叉树连接在一起，称为Merkle树，而不是直线链。在比特币中，交易取代了文件。在每个块的merkle树中，叶节点都是交易，每个内部节点基本上有两个指针组成。这个数据结构有两个重要属性:
- 最新块的hash作为摘要.对任何交易(叶节点)的更改都需要将更改一直传播到块的根,以及所有的后续块的根。因此,如果你知道最新的hash,你可以从不信任的来源下载余下的分类账并验证它没有改变。
- 某人可以有效地向您证明特定交易已包含在分类账中，这个用户只需要向您发送该交易块的少量节点（该是merkle树的节点）,以及每个后续块的少量信息。有效证明交易包含的能力对于性能和可伸缩性非常有用。


### 工作量证明PoW
#### 起源
   由Cynthina Dwork和Moni Naor于1992年创建的。他们的目标是阻止垃圾邮件。工作量证明适用于对抗所有三方。在Dwork 和 Naor的设计中，电子邮件收件人只会处理那些伴随发件人执行适量计算工作的证据的电邮件，因此也就是“工作量证明”。计算证明在普通计算机上可能需要几秒钟。因此，这对普通用户来说不会造成任何困难，但是希望发送一百万封邮件的垃圾邮件发送者需要几周的时间，在使用等效的硬件的情况下。

### Hashcash
   它没有暗门和中央权威，它只使用散列函数而不是数字签名。它基于一个简单的原则：散列函数在某些实际用途中表现为随机函数，这意味着找到散列到特定输出的输入唯一方法是尝试各种输入，直到产生所需输出位置。
   此外，找到散列到任意一组输出的输入的唯一方法是再次尝试散列不同的输入。


## blockchain reference
### [What is Blockchain Technology? A Step-by-Step Guide For Beginners](https://blockgeeks.com/guides/what-is-blockchain-technology/)

区块链是一个由化名为Satoshi Nakamoto所熟知的人及其他一群人的心血结晶。通过允许数字信息分发但不复制，区块链技术创造了一种新型互联网的支柱。最初设计为数字货币、比特币。

比特币被称为数字黄金，迄今为止，该货币的总价值接近90亿美元。区块链可以创造其他类型的数字价值。

### [a introduction to blockchain technology](https://bitsonblocks.net/2015/09/09/a-gentle-introduction-to-blockchain-technology/)
通常关于blockchain technology都是关于一种数据存储的主题.
具有以下特性的数据存储:
- 包含金融交易
- 实时多个系统被复制
- 存在于点对点网络中
- 使用密码和数字签名来证明身份、辨别真伪,并且有强制读写权限
- 被某些参与者写入
- 被某些参与者阅读,也可以被更广泛的读者阅读,并且有机制使其很难改变历史记录,或者至少可以很容易地检测到有人试图这样做

blockchain 是一系列技术的集合.
![avatar](https://bitsonblocks.files.wordpress.com/2015/09/blockchain_bag2.png)

blockchain与普通数据库的区别:blockchain系统是一个包含普通数据库和一些软件的包。这些软件可以添加新行，验证新航符合预先约定的规则,并通过网络监听和广播新行给他们的对等节点,确保所有对等节点在他们的数据库中有相同的数据。
![blockchain_approach](https://bitsonblocks.files.wordpress.com/2015/09/bitcoin_approach.jpg)