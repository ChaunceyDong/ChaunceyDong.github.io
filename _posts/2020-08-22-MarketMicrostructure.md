---
layout:     post
title:      Market Microstructure & HFT
subtitle:   市场微观结构_中国大学MOOC笔记
date:       2020-08-22
author:     Chauncey
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - High Frequency Trading
    - Notes
    - MOOC
    - Market Microstructure
---

课程笔记：[市场微观结构-中国大学MOOC](http://www.icourse163.org/course/SISU-1206672845?tid=1207003279)

笔记为第三章和第四章部分内容。

# 高频交易策略

## 做市交易

最为常见，缩小市场的spread（ask1 - bid1），以提供市场流动性

### spread该放多大

市场上的spread是由做市商决定/提供的，设置spread的时候要考虑以下三种订单处理成本

1. 手续费（买单卖单）
2. 存货成本（买单后，价格下行的成本；经济学的隐形成本）
3. 信息不对称成本（对方知道了做市商不知道的信息，toxic order）

## 方向交易

~ Momentum

HFT会基于**<u>订单流动</u>**信号进行交易

> 出现超级大买单 -》 预测市场上涨
>
> -》HFT 买Ask1，自己挂一个更高价格的新的"Ask1"
>
> -》由于市场上涨，新的Ask1会被其他人很快吃掉
>
> = 赚了新的自己挂的Ask1 - 旧的Ask1

该交易策略并未涉及任何传统的基本面分析。反倒是，例子中的那个超级大买单可能是某些交易对手基于基本面分析而挂出的，因此，HFT吃掉的是他们的一些利益（他们本来可以去买旧的便宜的Ask1，但是被HFT先一步，所以不得不和新的HFT挂出来的Ask1成交）。

## 偏差交易

~ Pairs trade

## 事件交易

Event-driven Strategy

<img src="https://github.com/ChaunceyDong/ChaunceyDong.github.io/blob/master/_posts_img/Event-driven_Strategy.jpg?raw=true" />

# 经典策略举例

## 全市场最优报价 & 闪电交易

> 1. 中国市场不存在NBBO，仅存在于美国市场
> 2. 美国市场的闪电交易现已被归为违规操作。

NBBO：美国市场中，同一支股票/衍生品，可以在30几个交易所买进卖出，执行价为这些交易所的最优价（卖：hit最高bid，买：hit 最低ask）

Flash Order：Exchange1闪现客户订单20毫秒，如没有做市商或其他
交易者愿意match，route此订单到NBBO的交易所。

## 快速行情

> 你看到的行情是我的成交

利用自身行情比其他交易者快的优势，比其他人

## 幌骗交易Spoofing

> 市场操纵，违法 & 利润大

想买的时候，先卖（挂大卖单，影响市场上其他的交易者，从而拉低价格。低了之后及时撤单），然后再买。反之亦然

例子：此时b/a = 8/11 。幌骗

需要成交大单时，市场过宽1.00 / 1.90，交 易员想买，但挂出offer的单子1.45卖，有人加入继续 改善成1.44，1.43……并根据其他参与者加入量逐渐 减少的情况判定何时终止并利用速度的优势撤单同时 挂上买单成交在最优的价位上。

## 手续费返还

提供流动性：发单后，order book变多；消耗流动性：发单后，成交了，order book变少了。

若提供了流动性，交易所会返佣，即使交易不赚钱，综合起来也是赚钱的。

## 冰山订单

交易所不会显示大单的全部volume

中国交易所没有提供冰山订单的选项。





