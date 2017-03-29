```flow
st=>start: Start|past:>http://www.google.com[black]
e=>end: End:>http://www.google.com
op1=>operation: My Operation|current
op2=>operation: Stuff|current
sub1=>subroutine: My Subroutine|request
cond=>condition: Yes
or No?|approved:>http://www.google.com
c2=>condition: Good idea|rejected
io=>inputoutput: catch something...|request

st->op1(right)->cond
cond(yes,right)->c2
cond(no)->sub1(left)->op1
c2(yes)->io->e
c2(no)->op2->
```

```flow
st=>start: 通话录音
op1=>operation: 音转字系统
op2=>operation: 录音文本
op3=>operation: 分类模型
cond=>condition: 是否销售误导
op4=>operation: 文本语料库
op5=>condition: 人工质检发现误导原因
e=>end: 对业务员进行相关培训

st(right)->op1->op2
op2->op3->cond
cond(no)->op5(yes)->e
cond(yes)->op4(left)->op3
op5(no)->op4
```