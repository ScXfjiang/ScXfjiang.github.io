---
layout:     post
title:      "[Compiler] Lexical Analysis"
toc:        true
categories: [Compiler]
---

## 1. The Role of Lexer
Compiler第一阶段的工作是**Lexical Analysis**，对应模块被称为**Lexical Analyzer (aka. Lexer, 词法分析器)**。Lexer的输入为Source Code，它将Source Code看作是"A stream of characters"；Lexer的工作主要是识别出Source Code满足特定Pattern的**Lexeme**，并将其转化成为后级模块**Parser**可识别的**Tokens**。

| Input of lexer  | A stream of characters |
| Output of lexer | A stream of tokens     |

### 1.1 Lexeme vs. Token
> **Lexeme**: A sequence of characters in the source program that matches the pattern for a token and is identified by the lexical analyzer as an instance of token.

> **Token**: A token is a pair consisting of a **token name** and an optional **attribute value**.

Token Name代表了Token的类别，例如identifier, keyword；Token Attribute是Token在编译过程中必要的属性，例如identifier的type和localtion。在Compiler系列文章中，除非特殊说明，Token和Token Name会混用。

### 1.2 [Example] A simplified Lexer

一门语言的Compiler支持如下Token：

| Token                | Examples    |
|:-------------------- |:----------- |
| **keyword**          | if, else    |
| **id (identifier)**  | pi, score   |
| **number**           | 3.1415926   |
| **literal**          | "core dump" |
| **assign_op**        | =           |
| **multi_op**         | *           |
| **exp_op**           | **          |

下面是使用该语言所写的一个statement：
```python
E = M * C ** 2
```

Lexer仅仅将上述代码看作是A stream of characters：

| \'E\' | \' \' | \'=\' | \' \' | \'M\' | \' \' | \'C\' | \' \' | \'*\' | \'*\' | \' \'  | \'2\' |


并将其解析成为A stream of tokens：
```
<id, pointer to symbol-table entry for E>
<assign op>
<id, pointer to symbol-table entry for M>
<mult op>
<id, pointer to symbol-table entry for C>
<exp op>
<number, integer value 2>
```

## 2. Languages and Its Operations
### 2.1 Definitions
**Alphabet**
> An **alphabet** is any finite set of symbols. Typical examples of symbols are letters,
> digits, and punctuation. **ASCII** is an important example of an alphabet; **Unicode**, 
> which includes approximately $$100,000$$ characters from alphabets around the world,
> is another important example of an alphabet.

**String**
> A **string** over an alphabet is a finite sequence of symbols drawn from that
> alphabet. In language theory, the terms **sentence** and **word** are often used
> as synonyms for string. The length of a string s, usually written $$\vert s\vert$$, is the
> number of occurrences of symbols in $$s$$. For example, banana is a string of
> length six. The **empty string**, denoted $$\epsilon$$, is the string of length zero.

**Language**
> A **language** is any countable set of strings over some fixed alphabet. This
> definition is very broad. Abstract languages like $$\phi$$, the empty set, or $$\{\epsilon\}$$, the
> set containing only the empty string, are languages under this definition. So
> too are the set of all syntactically well-formed C programs and the set of all
> grammatically correct English sentences, although the latter two languages are
> difficult to specify exactly. Note that the definition of language does not
> require that any **meaning** be ascribed to the strings in the language. Methods
> for defining the meaning of strings are discussed in Chapter 5 of Dragon Book.

总结来说，我们首先需要一个字符集Alphabet，在此之上定义String，而Language是String的一个Countable Set（Measure Theory中一个概念，不必纠结）。

### 2.2 Operations on Languages

| Operations                            | Definition and Notations                                          |
|:------------------------------------- |:-----------------------------------------------------------------:|
| Union of $$L$$ and $$M$$              |  $$L\bigcup M=\{s\ \vert\ s$$ is in $$L$$ or $$s$$ is in $$M\}$$  |
| Concatenation of $$L$$ and $$M$$      |  $$LM=\{st\ \vert\ s$$ is in $$L$$ and $$t$$ is in $$M\}$$        |
| Kleene closure of $$L$$               |  $$L^*=\bigcup^{\infty}_{i=0}L^i$$                                |
| Positive closure of $$L$$             |  $$L^+=\bigcup^{\infty}_{i=1}L^i$$                                |

这些概念比较简单，直接贴出Dragon Book中的一个例子：

#### 2.2.1 Example from Dragon Book
Let $$L$$ be the set of letters $$\{A, B, ..., Z, a, b, ..., z\}$$ and let $$D$$
be the set of digits $$\{1, 2, ..., 9\}$$. We may think of $$L$$ and $$D$$ in two, essentially
equivalent, ways. One way is that $$L$$ and $$D$$ are, respectively, the alphabets of
uppercase and lowercase letters and of digits. The second way is that $$L$$ and $$D$$
are languages, all of whose strings happen to be of length one. Here are some
other languages that can be constructed from languages $$L$$ and $$D$$.

* $$L\bigcup D$$ is the set of letters and digits -- strictly speaking the language
with 62 strings of length one, each of which strings is either one letter or
one digit.
* $$LD$$ is the set of 520 strings of length two, each consisting of one letter
followed by one digit.
* $$L^4$$ is the set of all $$4$$-letter strings.
* $$L^*$$ is the set of all strings of letters, including $$\epsilon$$, the empty string.
* $$L(L\bigcup D)$$ is the set of all strings of letters and digits beginning with a letter.
* $$D^+$$ is the set of all strings of one or more digits.


## 3. Regular Expression

## 4. The Lexical-Analyzer Generator: Lex

