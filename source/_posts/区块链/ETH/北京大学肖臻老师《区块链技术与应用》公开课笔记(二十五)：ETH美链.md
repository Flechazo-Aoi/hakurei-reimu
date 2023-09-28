---
title: 北京大学肖臻老师《区块链技术与应用》公开课笔记(二十五)：ETH美链
categories: 区块链
data: 2023-01-28  11:22:33
tags: 
- 区块链
- ETH
---

我们之前提到的The DAO的例子发生在16年，今天刚要讲的美链的例子发生在18年4月，出问题的智能合约叫做美链(beauty chain)

## 美链

美链是一个发行在以太坊上的代币。以太坊上有很多发行的代币，17年以太坊价格涨到很多，其中的主要原因就是有很多ICO(Initial Coin offering，代币首次发行)。这些发行的代币没有自己的区块链，而是以智能合约的形式运行在EVM上，发行代币的智能合约对应以太坊状态树上的一个节点，这个节点是一个合约账户，其中状态树中存储的账户余额为这个智能合约总共有多少以太币，至于合约里每个账户有多少代币，这个是作为存储树的变量存储在合约账户里。代币的存储，交易，销毁都是通过调用智能合约的函数实现的。比如你通过一个外部账户调用智能合约的函数转入1个以太币，那么对应的这个智能合约会给你代币账户上转一定数量的代币。

以太坊这个平台的出现，为各种代币的发行提供了方便，比如一些没有自己基础链的加密货币可以使用这个平台作为早期的交易平台。

![image-20230120194513606](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301201945819.png)

### batchTransfer函数

问题就出在这个batchTransfer函数上，我们看一下具体实现如下

![image-20230120194608158](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301201946332.png)

问题出在第三行

```solidity
uint256 amout = uint256(cnt) * count
```

这个乘法操作是可能溢出的，那么得到的结果amount就会使一个很小的数，后续从合约账户转出的以太币是amount的值，但是转给其他账户的代币是value的值，相当于空手套白狼。

### 攻击细节

如下图的例子，从合约账户转走0，给每个代币账户很多钱

![image-20230120195233582](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301201952772.png)



![image-20230120195400461](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301201954605.png)

### 攻击结果

![image-20230120195549116](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301201955250.png)

### 补救措施

当然，开发人员及时采取了措施，暂停了提现功能，没有让黑客获利

![image-20230120195509008](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301201955145.png)



### 题外话

实际上`solidity`提供了SafeMath库，使用库里的函数就不会出问题，如下图的乘法运算

![image-20230120200022778](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301202000876.png)

我们看到上面的batchTransfer函数的加操作和除操作都用了SafeMath库函数，唯独乘操作没用，不知道是不是有意为之，不过从结果来看，不像是有意为之。
