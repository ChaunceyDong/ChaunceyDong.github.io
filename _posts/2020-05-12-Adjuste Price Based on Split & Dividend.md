---
layout:     post
title:      Adjuste Price Based on Split & Dividend
subtitle:   Notes of AI for Trading, Project 1, Data Process
date:       2020-05-12
author:     Chauncey
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Python
    - DataFrame
    - AI for Trading
    - Pandas
---





By making additional adjustments to corporate action, split and dividend, we ensure that all price movements on our charts are caused by pure market forces - that is, the forces that Technical Analysis attempts to identify.

# Dividend

Cash Dividends, paid to shareholders that hold the stock before the ex-dividend date

Prices before the ex-dividend date need to be adjusted.

## Example

Stock Price 40

Dividend ​2

Price after Dividend 38

|      |      | ->   |
| ---- | ---- | ---- |
|      | 40   | 38   |
|      | 1    | 0.95 |

- multiply all historical prices before ex-dividend date by the factor of 0.95

# Split

Adjustments for stock splits are similar, divide the number of shares after the split by the number of shares before the split. 

## Example

To adjust for a 2-for-1 split, divide 1 by 2. The factor is 0.5. Multiply all historical prices prior to the split by 0.5.

![](https://raw.githubusercontent.com/ChaunceyDong/ChaunceyDong.github.io/master/_posts_img/1111.png)

