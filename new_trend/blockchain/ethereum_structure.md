Ethereum_structure
Bitcoin blocking state是以未使用交易输出的UTXO和持有该账户应用的钱包应用的程序参考来定义的.以太坊正式引入账户的概念作为协议的一部分。该账户既是起点也是终点。交易直接更新账户的余额,而不是像bitcoin UTXO那样维持状态。它允许传递在账户之间可能导致状态变化的值和消息和数据。这些转换都是使用交易来实现。这有两种类型的账户：Externally Owned Accounts 和 Contract Accounts。

Externally Owned Accounts:也称为EOA。它由私钥控制。可以参与到Ethereum网络中。它使用交易与blockchain中进行交互。

Contract Accounts：它由代码控制,并且只能由EOA激活。一个contract account代表一个智能合约。

每个账户都有coin余额。参与者注释和发送以太坊交易，或者发送交易触发智能合约代码，或者两者。 两个类型的交易都需要收费。账户必须有足够的余额来满足被触发的交易所需要的费用。费用以Wei来支付。Wei是以太坊中最低的命名。one Ether = 10^18 Wei。在以太坊中的交易包含邮件的收件人、授权转让的发件人的数字签名、要转让的Wei的数量、可选的数据字段或payload that contains a message to a contract、startgas(允许交易的最大步骤数)、gas price(fee of computation)。

我们来看一下以太坊的实际交易情况。经过的时间值可能与此记录时记录的时间值不同。观察交易散列，链条高度，时间戳记，from and to accounts（来往账户），价值运输，天然气限制，使用的天然气，交易收据，success in this case and nonce。这个特定的交易是执行在智能合约上的。当然，智能合同执行会产生费用，可用金额由various gas fields指定。

以太坊块结构有一个头部，事务和 runner-up block header。block详情，一个真实的以太坊块在高度4 4 4 6 3 0 8.你可以在给定的链接上实际查看这个特定的块。它显示高度，时间戳，块散列，以前的散列，难度和总难度，大小，使用的气体，气体限制，随机数和块奖励。总而言之，账户是以太坊协议的基本单位。外部拥有账户和智能合约账户。以太坊交易不仅包括传递Ethers的域，还包括调用Smart合同的信息。以太坊区块包含通常以前的区块散列，随机数，交易细节，但也包含有关天然气或收费限制，智能合同和runner-up block header的状态。

## [Ethereum:smart contract white page](https://github.com/ethereum/wiki/wiki/White-Paper)
