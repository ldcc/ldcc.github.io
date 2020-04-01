---
layout: post
title: 基于数论的那些加密算法都用到了哪些数学定理
categories: mathematics
tags: mathematics algorithm cryptographic-algorithms
date: 2020-03-11
mathjax: true
hide: true
---

return 0;

高斯时钟，费马小定理，欧拉-费马定理

该计算过程类似传统的 RSA 暴力破解过程，要理解这个过程需要先复习一下算术基本定理：
任意一个大于 1 的自然数 $N$，如果 $N$ 不为质数，那么 $N$ 可以唯一分解成有限个质数的乘积：

$$
N = \prod^{p \in \mathbb{P}} p^{a},
\qquad
a \in \{0, \mathbb{N}\}
$$

两个素数的积，其有效公约数只有这两个素数

$$
N = (p-1) \times (q-1) + 1,
\qquad
\D = \F - \E
\\
\C^\F \equiv \C \mod p \times q
\\
\C^\E \times \C^\D \equiv \C \mod p \times q
$$

关于「公因数分解」的「整除过程」与「整除判断」的 CPU 指令优化 -> 矿机汇编指令优化 





这里简单列举一些较为知名的哈希函数：

- __CRC__(Cyclic redundancy check)：
  发布于 1961 年，由于易于二进制的计算机硬件使用、容易进行数学分析并且尤其善于检测传输通道干扰引起的错误。
  如果确定数据不会受到蓄意的损坏或篡改，可以改用一些安全性低但效率高的校验和算法，比如 CRC。
- __MD5__(Message Digest Algorithm5)：
  发布于 1992 年，曾经被广泛使用的一种加密函数，用以提供消息的完整性保护。
  2004年，MD5 被证实算法无法防止碰撞(collision)，因此已不适用于安全性认证。
- __SHA__(Secure Hash Algorithm)：SHA 系列算法由美国国家安全局(NSA)设计，并由美国国家标准与技术研究院(NIST)发布为联邦数据处理(FIPS)标准。
  - __SHA-0__：于 1993 年发布，当时被称做安全散列标准(Secure Hash Standard)，发布之后很快就被 NSA 撤回，是 SHA-1 的前身。
  - __SHA-1__：于 1995 年发布，SHA-1 在许多安全协议中广为使用，包括 TLS, SSL, PGP, SSH, S/MIME 及 IPsec ，曾被视为是 MD5 的后继者。
    但 SHA-1 的安全性已经不被大多数的加密场景所接受。2017 年荷兰密码学研究小组 CWI 和 Google 正式宣布攻破了 SHA-1。
  - __SHA-2__：于 2001 年发布，包括 SHA-224, SHA-256, SHA-384, SHA-512, SHA-512/224, SHA-512/256。
  - __Keccak__：2012 年 10 月，Keccak 被选为 NIST 散列函数竞赛的胜利者。
  - __SHA-3__：SHA-3 原名就是 Keccak，2014 年 NIST 发布了 FIPS 202 的草案，2015 年 8 月，SHA-3 由 NIST 通过 FIPS 202 正式发表。

> 以上内容摘选自维基百科。
