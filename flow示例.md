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
st=>start: 开始
e=>end: 结束

st->e
```