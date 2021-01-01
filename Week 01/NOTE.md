# 学习笔记
### 方案一
#### 1、使用二维数组表示井字棋棋子
```javascript
let pattern = [
  [0, 0, 0],
  [0, 0, 0],
  [0, 0, 0],
];
```
#### 2、编写drawChessboard绘制棋盘逻辑
2.1 绘制棋盘格子及棋子，使用双层for循环遍历pattern字段
2.2 pattern[i][j]代表第j行第i列单元格棋子状态：0代表空，1代表⭕，2代表❌

```javascript
// 空|⭕|❌ 0 | 1 | 2
let color = 1;

function drawChessboard(pattern) {
  let board = document.getElementById("board");

  board.innerHTML = "";

  for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
      let cell = document.createElement("div");
      cell.classList.add("cell");
      cell.innerText =
        pattern[i][j] === 2 ? "❌" : pattern[i][j] === 1 ? "⭕" : "";
      if (pattern[i][j] == 0) {
        cell.addEventListener("click", () => userMove(i, j));
      }
      board.appendChild(cell);
    }
    board.appendChild(document.createElement("br"));
  }
}
```

#### 3、编写checkWin判定胜负逻辑
3.1 检测行，pattern[i][j] !== color
3.2 检测列，pattern[j][i] !== color
3.3 检测对角：左上 -> 右下
3.4 检测对角：右上 -> 左下

```javascript
// 检测胜负
function checkWin(pattern, color) {
  // 检测行
  for (let i = 0; i < 3; i++) {
    let win = true;
    for (let j = 0; j < 3; j++) {
      // 行棋子不同，必定不会获胜
      if (pattern[i][j] !== color) {
        win = false;
      }
    }
    // 行棋子相同，则获胜
    if (win) {
      return true;
    }
  }

  // 检测列
  for (let i = 0; i < 3; i++) {
    let win = true;
    for (let j = 0; j < 3; j++) {
      // 列棋子不同，必定不会获胜
      if (pattern[j][i] !== color) {
        win = false;
      }
    }
    // 列棋子相同，额获胜
    if (win) {
      return true;
    }
  }

  // 检测对角：左上 -> 右下
  {
    let win = true;
    for (let i = 0; i < 3; i++) {
      // 棋子不同，必定不会获胜
      if (pattern[i][i] !== color) {
        win = false;
      }
    }
    // 棋子相同，则获胜
    if (win) {
      return true;
    }
  }

  // 检测对角：右上 -> 左下
  {
    let win = true;
    for (let i = 0; i < 3; i++) {
      // 棋子不同，必定不会获胜
      if (pattern[2 - i][i] !== color) {
        win = false;
      }
    }
    // 棋子相同，则获胜
    if (win) {
      return true;
    }
  }
  return false;
}
```

#### 4、编写willWin逻辑
```javascript
function willWin(pattern, color) {
  for (i = 0; i < 3; i++) {
    for (j = 0; j < 3; j++) {
      // 如果已经落子，则不再统计范围内
      if (pattern[i][j] !== 0) {
        continue;
      }
      let temp = clone(pattern);
      temp[i][j] = color; // 模拟落子
      // 当检测可以赢的时候，返回这个位置
      if (checkWin(temp, color)) {
        return [i, j];
      }
    }
  }
  return null;
}
```

#### 5、人机交互逻辑
```javascript
// 寻找最优位置
function bestChoice(pattern, color) {
  let p;
  // 如果我方可以赢，则返回位置和结果 1
  if ((p = willWin(pattern, color))) {
    return {
      point: p,
      result: 1,
    };
  }

  // -1 0 1
  // 如果我方赢不了，则找到对方能赢的位置，然后我方去封堵
  let result = -2;
  let point = null;
  for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
      if (pattern[i][j] !== 0) {
        continue;
      }
      let temp = clone(pattern);
      temp[i][j] = color;

      let opp = bestChoice(temp, 3 - color);
      // 找到最优解 -1 > -2
      // 0 > -2
      if (-opp.result > result) {
        result = -opp.result;
        point = [i, j];
      }
    }
  }
  return {
    point: point,
    result: point ? result : 0,
  }
}
```
[代码完整链接](https://github.com/clwu1994/Frontend-08-Template/tree/main/Week%2001)

### 方案二
#### 1、使用一维数组表示井字棋棋子
```javascript
let pattern = [0,0,0,0,0,0,0,0,0];
```
#### 2、编写drawChessboard绘制棋盘逻辑不变，pattern[i][j]改为pattern[i*3+j]
#### 3、编写checkWin判定胜负逻辑
3.1 检测行，pattern[i*3+j] !== color
3.2 检测列，pattern[j*3+i] !== color
3.3 检测对角：左上 -> 右下
3.4 检测对角：右上 -> 左下
```javascript
function checkWin(pattern, color) {
  // 行
  for (let i = 0; i < 3; i++) {
    let win = true;
    for (let j = 0; j < 3; j++) {
      if (pattern[i * 3 + j] !== color) {
        win = false;
        break;
      }
    }
    if (win) {
      return true;
    }
  }
  // 列
  for (let i = 0; i < 3; i++) {
    let win = true;
    for (let j = 0; j < 3; j++) {
      if (pattern[i + j * 3] !== color) {
        win = false;
        break;
      }
    }
    if (win) {
      return true;
    }
  }
  // 正斜 index 0/4/8
  {
    let win = true;
    for (let i = 0; i < 3; i++) {
      if (pattern[i * 3 + i] !== color) {
        return false;
        break;
      }
    }
    if (win) {
      return true;
    }
  }

  // 反斜 index 2/4/6
  {
    let win = true;
    for (let i = 0; i < 3; i++) {
      if (pattern[2 * i + 2] !== color) {
        return false;
      }
    }
    if (win) {
      return true;
    }
  }
  return false;
}
```