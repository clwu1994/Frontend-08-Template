# 学习笔记
## LL算法
### 四则运算

*词法定义：*
- TokenNumber：
  - 1234567890的组合
- Operator：+、-、*、/之一
- Whitespace: <SP>
- LineTerminator：<LF><CR>

*词法定义：*
```
<Expression>::=
  <AdditiveExpression><EOF>

<AdditiveExpression>::=
  <MultiplicativeExpression>
  |<AdditiveExpression><+><MultiplicativeExpression>
  |<AdditiveExpression><-><MultiplicativeExpression>

<MultiplicativeExpression>::=
  <Number>
  |<MultiplicativeExpression><*><Number>
  |<MultiplicativeExpression></><Number>
```