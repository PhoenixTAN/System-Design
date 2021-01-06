# PageRank

## Reference
https://en.wikipedia.org/wiki/PageRank

## Definition
PageRank (PR) is an algorithm used by Google Search to **rank web pages** in their search engine results.

PageRank is a way of **measuring the importance of website pages**. According to Google:

PageRank works by counting the number and quality of links to a page to determine a rough estimate of how important the website is. The underlying assumption is that more important websites are likely to receive more links from other websites.

![alt text](./images/PageRanks-Example.jpg)

## Data structure
PageRank的结果来源于一种基于图论的数学算法。它将万维网上所有的网页视作节点（node），而将超链接视作边（edge），并且考虑到了一些权威的网站，类似CNN。

每个网页的权重值大小被递归地定义，依托于所有链接该页面的页面的权重值。例如，一个被很多页面的链接的页面将会拥有较高的权重值（high PageRank）。

## Algorithm

### Simplified Version
假设一个由4个网页组成的集合：A，B，C和D。

同一页面中多个指向相同的链接视为同一个链接，并且每个页面初始的PageRank值相同，最初的算法将每个网页的初始值设定为1。

但是在后来的版本以及下面的示例中，为了满足概率值位于0到1之间的需要，我们**假设这个值是0.25**。

在每次迭代中，给定页面的PR值（PageRank值）将**均分**到该页面所链接的页面上。

#### 情况1
如果所有页面都只链接至A，那么A的PR值将是B，C及D的PR值之和，即：

$$ PR(A)=PR(B)+PR(C)+PR(D) = 0.75 $$

#### 情况2
重新假设B链接到A和C，C链接到A，并且D链接到A,B,C。最初一个页面总共只有一票。所以B给A ,C每个页面半票。以此类推，D投出的票只有三分之一加到了A的PR值上：

$$PR(A)
={\frac {PR(B)}{2}}+{\frac {PR(C)}{1}}+{\frac {PR(D)}{3}} \\
=0.125 + 0.25 + 0.083 = 0.458
$$


