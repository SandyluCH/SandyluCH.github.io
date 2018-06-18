Validation, verification,consensus, Immutable Recording lead to the trust and security of Blockchain.

参考文档：
[Bitcoin's Academic Pedigree](https://queue.acm.org/detail.cfm?id=3136559)
在每个区块中，文件可以用哈希指针的二叉树连接在一起，称为Merkle树，而不是直线链。在比特币中，交易取代了文件。在每个块的merkle树中，叶节点都是交易，每个内部节点基本上有两个指针组成。

### 工作量证明PoW
#### 起源
   由Cynthina Dwork和Moni Naor于1992年创建的。他们的目标是阻止垃圾邮件。工作量证明适用于对抗所有三方。在Dwork 和 Naor的设计中，电子邮件收件人只会处理那些伴随发件人执行适量计算工作的证据的电邮件，因此也就是“工作量证明”。计算证明在普通计算机上可能需要几秒钟。因此，这对普通用户来说不会造成任何困难，但是希望发送一百万封邮件的垃圾邮件发送者需要几周的时间，在使用等效的硬件的情况下。

### Hashcash
   它没有暗门和中央权威，它只使用散列函数而不是数字签名。它基于一个简单的原则：散列函数在某些实际用途中表现为随机函数，这意味着找到散列到特定输出的输入唯一方法是尝试各种输入，直到产生所需输出位置。
   此外，找到散列到任意一组输出的输入的唯一方法是再次尝试散列不同的输入。


参考文档：[How a Bitcoin Transaction Works](https://www.ccn.com/bitcoin-transaction-really-works/)