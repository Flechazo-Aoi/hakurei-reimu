---
title: 北京大学肖臻老师《区块链技术与应用》公开课笔记(十二)：BTC匿名性
categories: 区块链
date: 2023-01-09  15:22:33
tags: 
  - 区块链
  - BTC
---

一般来说,匿名性多与隐私保护相关。但实际上，比特币中的匿名并非真正的匿名，而是假的匿名，注册账户可以用一个化名，不要求实名。实际上，比特币与纸币相比，纸币的匿名性更好，因为纸币并没有对个人信息的标记。也正是因为其匿名性，很多非法交易采用现金交易。但现金存在保管、运输等各个方面的不便。

早期银行也不强制要求实名，只要有一个化名即可，早期银行与比特币系统相比，匿名性甚至更好，因为银行的交易数据等并不公开，而比特币系统中的数据是完全公开的。

## BTC系统中什么情况会破坏其匿名性？

### 个人的多个账户可能被关联

用户可以在每次交易时使用不同的地址账户来避免被查到真实信息，但这些地址账户可以被关联起来

表面上看，每次交易可以更换公私钥对，从而每次都是新的账户，具有很强的匿名性。但实际上，这些账户在一定情况下，是可以被关联起来的。

我们知道一个订单可以有多个输入和多个输出，如下图，那么addr1和addr2很可能是同一个人所持有的账户，因为该人同时拥有这两个私钥的地址。(一个账户中的钱可能不够支付)。同时输出地址也能和输入地址有联系，比如下图的例子，我们不难推断出addr3可能是商家地址，addr4可能是付款者用的找零地址

![image-20230108095026176](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301080950305.png)

### 个人账户与现实世界身份可能被关联

任何使得BTC和实体世界中交易的操作都有可能泄露用户真实身份，

比如最明显的就是资金的转入转出。要得到BTC，如果用钱买，就会与实体世界进行交互。想要将BTC转为现实中的货币，也同样需要与实体世界交互。

> 在很多国家，都有防洗钱法。如何防范不法分子采用BTC进行洗钱呢？其实很简单，只需要盯住资金转入转出链即可。对于大额资金转入BTC或将大量BTC转为现实货币，很难逃避司法金融机构的监管。

再比如使用BTC支付，某些商家接受用BTC进行支付，例如可以用BTC购买咖啡等(bad idea，因为BTC交易延迟高(网络传播延迟，要等6个确认块)，不如信用卡)。在进行支付时候，便和个人账户建立了联系，从而会泄露掉个人信息。

> 之前有一个机构为了方便学者研究，把信用卡消费记录中账户取哈希，个人信息大码，然后公开。这时有人进行研究，能否通过这些加密的信息找到对应的真实身份，答案是肯定的。比如你是A的熟人，A刚好在周四早上在你的前面刷卡买了菜，你就可以从交易列表中找到对应时间对应超市的交易记录，从而排除绝大多数交易，这样筛选几次很容易得到真实信息。而比特币交易信息是完全公开的，所以只要你与现实世界产生交互，都有很大概率泄露信息

也就是说，BTC并不是具有很好的匿名性。保持最好的便是其开发者中本聪，其参与BTC时间最长，全世界都想知道他是谁。但实际上，中本聪的比特币没有花出去，没有与现实世界产生联系，这也使得我们难以发现他具体是谁。

> 以前美国有一个skil road网站，主要用于匿名支付，采用各类可以躲避监管的方法(因为售卖的都是违禁品)。但运行没有几年就被查封，其老板当时赚取了许多比特币，但由于其担心被发现，这些钱实际中一个都不敢花，在美国仍然过的是非常简朴的生活。最终据说由于在同一电脑上登录现实社会账户和非法网站上账户，从而被抓(具体原因未公开)。
> skil road被查封后，有人开通了skil road2，运行没有几年又被查封。

因此，可见互联网并非法外之地。**如果想要干坏事，基本都能被查到**。

## BTC匿名性有多好？如何提高匿名性？

匿名的本质是不想要暴露身份。问题是，你要对谁隐藏身份。而对于普通人来说，假如我要向我的朋友隐藏我有多少个比特币，BTC的现有机制已经足够保持个人隐私了。但如果涉及违法，也就是你想要向行政机关隐藏身份，是很困难的

那么可以采取哪些方法尽可能提高匿名性？

如下图，比特币是一个应用层协议，下面是网络层，首先在网络层上传输要保持匿名性。IP地址跟现实世界的身份有很强的关联性，可以通过多路径转发来实现，比如洋葱路由(TOR)。

![image-20230108105957404](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301081059469.png)

其次是应用层，我们前面说破坏匿名性的一个原因是：同一个用户的不同账号比较容易关联起来，那么把一堆人的币混在一起就好(coin mixing)，这个在其他要隐藏身份的领域也很常用。现在有些coin mixing机构，可以提供这个服务，实现起来有些难度，但是机构也要匿名，机构的可信性有待商榷。

实际上，暴露用户隐私正是由于区块链的公开性和不可篡改性。不可篡改性对于隐私保护，实际上是灾难性的。

## 零知识证明

**零知识证明：一方（证明者）向另一方（验证者）证明某一个陈述是正确的，但不需要透露除该陈述是正确的之外的任何信息。**

例如：A想要向B证明某一账户属于A，这说明A知道该账户的私钥。但不可能通过A公布私钥的方法来证明，该账户确实属于A。因此，A可以产生一个账户签名，B通过公钥对签名进行验证。(实际上该证明是否属于零知识证明存在争议，因为泄露了用私钥产生的签名)

![image-20230108112134520](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301081121635.png)

## 同态隐藏

- 第一个性质，说明如果有E(X)=E(y)，则必然有x=y。(无碰撞性，我们比特币中提到的无碰撞性是无法严格数学证明的)
- 第二个性质，说明加密函数不可逆。知道加密值，无法反推出密码值(hiding property)。
- 第三个性质，最为重要，称为同态运算。说明对加密后的函数值进行某些代数运算，等价于对输入直接进行代数运算再加密。

![image-20230108112244386](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301081122466.png)

举个例子

![image-20230108112531709](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301081125760.png)

![image-20230108112704423](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301081127512.png)

不足：bob可以暴力求解x，y

## 盲签

盲签名是一种特殊的数字签名技术。盲签名因签名的人看不到所签署文件的具体内容而闻名，它有两个显著的特点：一是签名者对消息的内容是不可见的 ;二是签名被公开后，签名者不能追踪签名。

> 为什么要这么做呢？
>
> 我们之前举得央行发行电子货币两种方法，第一种无法防范双花攻击不能采用，第二种，央行维护一个数据结构，所有交易都要经过央行，由央行根据交易修改数据结构，中心化机构(央行)等于啥都知道，如果我们既想我们的交易信息依赖于银行等第三方机构，又想第三方机构不知道交易具体内容，就可以采用盲签

![image-20230108113340103](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301081133216.png)

## 零币和零钞——专门为匿名性设计的货币

![image-20230108113457919](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202301081134050.png)

零币在花费的时候，只需要用零知识证明来证明所花掉的币是系统中存在的某一个合法的币，但不用透露具体花掉的是系统中哪一个币。这样就破坏了关联性。
当然，这类货币并非主流加密货币，因为其为了设计匿名性，付出了一定代价，而且，需要强匿名性的用户并不多。

从数学上看，零币和零钞是安全的。但其并不是百分之百的匿名，其并未解决与系统外部实体发生交互时对匿名性的破坏。
