## [a introduction to blockchain technology](https://bitsonblocks.net/2015/09/09/a-gentle-introduction-to-blockchain-technology/)
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


### Public vs Private Blockchain
- Public blockchains
分类账在两种情况下是公共的:
  - 没有任何其他人授权的可以写数据
  - 没有任何其他人授权的可以读数据
public blockchains,我们通常是指任何人可以写数据。

- private blockchain
反过来,private blockchain就是需要参与者被信任和被知悉.





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
