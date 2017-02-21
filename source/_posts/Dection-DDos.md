---
title: Dection-DDos
date: 2016-11-10 21:14:46
tags:
categories: 机器学习
---
最近研究了一篇论文，关于检测DDos攻击，使用了深度学习中 栈式自编码的算法，现在简要介绍一下内容
[论文下载](http://oelvsay9f.bkt.clouddn.com/ddos.pdf)

[讨论班讲解pdf-by airghc](http://oelvsay9f.bkt.clouddn.com/%E8%AE%A8%E8%AE%BA%E7%8F%ADairghc.pdf)

[ppt](http://oelvsay9f.bkt.clouddn.com/al_ddos.pdf)

DDOS: Distributed Denial of Service(分布式拒绝服务)
Purpose:
disrupting transactions and access to databases
The attack on the application layer can disrupt services such as the retrieval of information or
search function[4] as well as web browser function, email services and photo applications
Accompanies:
Usually The attack had been carried out in two stages.First, Transport layer DDos attack was
carried out to get access on to the web server In the second stage an AL-DDos attack was
launched.
Difference:
以去银行办业务举例:
网络层 ddos 是让去往银行的道路交通变得拥堵,无法使正真要去银行的人到达;常利用协议为网络层的,
如 tcp(利用三次握手的响应等待及电脑 tcp 连接数限制)等
应用层 ddos 则是在到达银行后通过增办、询问业务等等各种“合法要求”来消耗银行的业务资源,如利用
http(查看所有网页、占用资源大的网页如:视频等或让网站处理复杂数据如:校验、计算等)
两者本质都是消耗资源,使服务器无法为真实用户提供服务
Mitigation 缓和
hijacked 被劫持,被绑架
1 很严重,检测的难度很大,很难预先阻止,不同的几种 ddos 攻击类型
2 相关的工作,介绍了目前基于模式识别的检测方法,主要分成两类,序列化模式识别和统计模式识别,
同时说明了目前方法的缺陷
3 所有已经存在的方法不能检测所有类型的 al-ddos 攻击,因为很少特征能够用于分类正常流量和攻击流
量,所以作者使用了深度学习的方法进行特征学习,从有限的样本中得到更多的抽象特征,本论文使用了
栈式自编码的算法进行特征学习
4 目标方法先不说,等后面详细介绍
5 结论 经过统计结果得到方法的正确率达到了 98.99%
Three types:
Session flooding: the attacker sends a session connection request at a huge rate than benign
user
Request flooding: in on session, the attacker make a huge number of requests than benign
user
Asymmetric Attack: the attacker makes requests with very high workloads such as
downloading of big files or response to some database intensive query
Sequential pattern recognition:
HsMM: Hidden Semi Markov Model (隐半马尔可夫模型)描述正常网页用户的浏览习惯从合法用户所做
出的请求序列中学习得到,从而预测合法的用户要获取一个页面所做出的一些序列化请求顺序,通过计算
正常用户所做行为的熵当做一个参数去测量用户的合法性
Random walk graph:创建正常用户的随机行走图,通过使用雅可比行列式测量待检测的用户行为和刚才
的随机行走图之间的相似程度来判断合法性
Statistical pattern recognition:
Trust Management: 通过访问用户的 ip 地址历史记录,分配给用户信任等级,如果用户在历史记录中表
现良好,那么就会分配给他更高的信任等级Hierarchical Clustering(分层群聚):检测会话泛洪攻击有四个特征
单次会话中平均请求对象大小
请求速度
会话中对象的访问频率
平均转换概率
RFV: 计算正常 traffic 和攻击 traffic 的熵的不同来检测 AL-DDoS 攻击
AutoEncoder:
Deep Learning 最简单的一种方法是利用人工神经网络的特点,人工神经网络(ANN)本身就是具有层
次结构的系统,如果给定一个神经网络,我们假设其输出与输入是相同的,然后训练调整其参数,得到每
一层中的权重。自然地,我们就得到了输入 I 的几种不同表示(每一层代表一种表示),这些表示就是特
征。自动编码器就是一种尽可能复现输入信号的神经网络。
如上图,我们将 input 输入一个 encoder 编码器,就会得到一个 code,这个 code 也就是输入的一个表
示,那么我们怎么知道这个 code 表示的就是 input 呢?我们加一个 decoder 解码器,这时候 decoder
就会输出一个信息,那么如果输出的这个信息和一开始的输入信号 input 是很像的(理想情况下就是一样
的),那很明显,我们就有理由相信这个 code 是靠谱的。所以,我们就通过调整 encoder 和 decoder
的参数,使得重构误差最小,这时候我们就得到了输入 input 信号的第一个表示了,也就是编码 code 了。
因为是无标签数据,所以误差的来源就是直接重构后与原输入相比得到。
那上面我们就得到第一层的 code,我们的重构误差最小让我们相信这个 code 就是原输入信号的良好表达
了,或者牵强点说,它和原信号是一模一样的(表达不一样,反映的是一个东西)。那第二层和第一层的
训练方式就没有差别了,我们将第一层输出的 code 当成第二层的输入信号,同样最小化重构误差,就会
得到第二层的参数,并且得到第二层输入的 code,也就是原输入信息的第二个表达了。其他层就同样的
方法炮制就行了
Stacked autoencoder
