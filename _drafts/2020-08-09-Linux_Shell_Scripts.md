---
layout:     post
title:      Linux Shell Scripts
toc:        true
---
## if-else
### 1. syntax
#### 1.1 basic
与其他编程语言的if-else语句不同，shell script以shell command的**exit code**为条件做分支选择。每个shell command都有一个exit code，使用
```bash
echo $?
```
获取，exit code为$$0$$时表示shell command被正常执行，这时执行then后面的语句。shell script的exit code为最后一个shell command的exit code。

```bash
if command; then
  commands
fi
```

```bash
if command; then
  commands
else
  commands
fi
```

```bash
if command; then
  commands
elif command; then
  commands
fi
```

#### 1.2 condition test
bash提供test命令做条件判断，方括号是test命令的语法糖，方括号中的condition为True时方括号的exit code为$$0$$，执行then后面的命令，否则执行else后面的命令。
```bash
if [ condition ]; then
  commands
else
  commands
fi
```
注意，第一个方括号之后和第二个方括号之前必须加上一个空格，否则会报错。

condition test可判断三类条件：
* 数值比较
* 字符串比较
* 文件比较

##### 1.2.1 数值比较

| condition | description |
| --------- | :---------: |
| n1 -eq n2 |      =      |
| n1 -ge n2 |     >=      |
| n1 -gt n2 |      >      |
| n1 -le n2 |     <=      |
| n1 -lt n2 |      <      |
| n1 -ne n2 |     !=      |

注意，在test命令的数值比较中只可比较整数，不要比较浮点数。

##### 1.2.2 字符串比较

| condition    |    description     |
| ------------ | :----------------: |
| str1 = str2  |         =          |
| str1 != str2 |         !=         |
| str1 < str2  |         <          |
| str1 > str2  |         >          |
| -n str       | 检查str长度是否非0 |
| -z str       | 检查str长度是否为0 |

##### 1.2.3 文件比较
略

### 2. examples
