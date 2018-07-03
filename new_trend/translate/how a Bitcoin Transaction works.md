## [how a Bitcoin Transaction works](https://www.ccn.com/bitcoin-transaction-really-works/)
比特币交易是一个经过签名的数据，它被广播到网络，如果有效，最终进入到区块链的一个区块。比特币交易的目的是将一定数量的比特币的所有权转移到比特币地址。当您发送比特币时，您的钱包客户端会创建一个单一的数据结构，即比特币交易，然后广播到网络。网络上的比特币节点将中继和重新广播事务，如果事务有效，节点将把它包含在他们正在挖掘的块中。通常，在10到20分钟内，交易将与区块链中的一个区块中的其他交易一起被包括在内。此时接收者能够看到他们钱包中的交易金额。

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
你创建一个全新的钱包，并及时受到三个0.01,0.2和3BTC的金额，如下所示，您将3BTC发送到钱包相关联的地址，并由Alice向另一个地址付款。钱包报告的越为3.21BTC,但如果你真的偷看钱包里面，你会看到，不是321 mil satoshi ,而仍然是三个不同金额，受到的比特币金额不会混合，仍然保持分开作为发送钱包的确切金额。上述中的三个金额称为其原始交易的输出。比特币钱包始终保持输出分离和独立。

![avatar](https://248qms3nhmvl15d4ne1i4pxl-wpengine.netdna-ssl.com/wp-content/uploads/2014/07/Bitcoin_Transactions_Alice.png)
在钱包中,这三个数不是合在一起的,它们是独自分开的,放在一起由原始的transaction:0.01 , 0.2 和3 .
受到的比特币金额不会混合
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
