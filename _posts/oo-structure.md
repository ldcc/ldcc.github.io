---
layout: post
title: 面向对象的设计
category: Interpreter
---

## DRAFT

{% highlight racket %}

(struct id maybe-super (field ...)
  struct-option ...)

maybe-super   = 
              | super-id
 	 	 	 	 
field         = field-id
              | [field-id field-option ...]

struct-option = #:mutable
              | #:super super-expr
              | #:inspector inspector-expr
              | #:auto-value auto-expr
              | #:guard guard-expr
              | #:property prop-expr val-expr
              | #:transparent
              | #:prefab
              | #:authentic
              | #:name name-id
              | #:extra-name name-id
              | #:constructor-name constructor-id
              | #:extra-constructor-name constructor-id
              | #:reflection-name symbol-expr
              | #:methods gen:name method-defs
              | #:omit-define-syntaxes
              | #:omit-define-values
 	 	 	 	 
field-option  = #:mutable
              | #:auto

{% endhighlight %}




