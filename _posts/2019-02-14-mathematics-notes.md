---
layout: post
title: 数学学习笔记
category: HIDE
date: 2019-02-14
---

GO!

$$
\DeclareMathOperator{infer}{\Gamma \vdash}
\DeclareMathOperator{sigmav}{\vec{\sigma}}
\newcommand{e}[1]{\hat{e}_#1}
\newcommand{mag}[1]{\|#1\|}
\sigmav ::= \mathbb{R}^n \left| \begin{bmatrix} a_1 & \dots & a_n \end{bmatrix} \right| a_1\e{1} + \dots + a_n\e{n}
$$$$$$

$$
\frac{\infer \mag{\vec{v} : \sigmav} \equiv \mag{\hat{u} : \sigmav}}
     {\infer k\mag{\vec{v}} \equiv k}
\qquad
\text{where } \hat{u} \text{ is Unit-Vector}
$$

$$
\frac{\infer k\mag{\vec{v} : \sigmav} \equiv k \quad \infer k\vec{v} : \sigmav}
     {\infer \mag{k\vec{v}} \equiv k}
$$

$$
\vec{v} ::= \frac{k\vec{v}}{k}
$$$$$$

$$
\text{by Substitution}
\qquad
\frac{\infer k\mag{\vec{v} : \sigmav} \equiv k \equiv \mag{k\vec{v} : \sigmav}}
     {\infer \vec{v} \equiv \frac{k\vec{v}}{k\mag{\vec{v}}} \equiv \frac{k\vec{v}}{\mag{k\vec{v}}}}
$$

$$
\text{formally denote as}
\qquad
\hat{u} \equiv \frac{\vec{u}}{\mag{\vec{u}}}
$$
