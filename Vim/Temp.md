 normal模式
## 移动方式
hjkl：前下上右
gg：跳转到第一行
{lineno}gg：跳转至lineno行
G：跳转至最后一行
w：`word`，跳转到下一个单词开头
b：`back`，跳转到上一个单词开头
e：`end`，跳转到下一个单词结尾
ge：e的反向，跳转到上一个单词结尾
WBE：对应的单词是连续的非空字符
## 搜索
### 行内搜索
f{char}/t{char}：跳转到下一个出现`char`字符出现的处/出现前
；/，：快速向前/向后重复`ft`查找
F{char}/T{char}：就是小写的相反
### 文件中搜索
/{pattern}：跳转到本文件中下一个出现`pattern`的位置
？{pattern}：跳转到本文件中上一个出现`pattern`的位置
`pattern`可为正则表达式
\*\==`/{pattern}`,`pattern`为光标下的单词
n：快速重复`/`查找
## 基于标记的移动
m{mark}：把当前位置标记为`mark`
\`{mark}：跳转到`mark`位置
`mark`：是`a-z`
\` \`：上次跳转前的位置
\`.：上次修改的位置
\`^：上次插入的位置


- x：删除
- dd：删除整行
- u：撤销
- a：在当前光标之后输入
- A：在行尾输入
- o：至下一行另起一行
- O：至上一行另起一行
- c：删除整行
- S\==\^C
- s：删除光标下的字符，然后进入插入式

" Test "," Test2 "


