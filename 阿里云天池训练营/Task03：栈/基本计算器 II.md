### 167.基本计算器 II

难度：中等

给你一个字符串表达式 `s` ，请你实现一个基本计算器来计算并返回它的值。

整数除法仅保留整数部分。

 

**示例 1：**

```
输入：s = "3+2*2"
输出：7
```

**示例 2：**

```
输入：s = " 3/2 "
输出：1
```

**示例 3：**

```
输入：s = " 3+5 / 2 "
输出：5
```

 

**提示：**

- `1 <= s.length <= 3 * 105`
- `s` 由整数和算符 `('+', '-', '*', '/')` 组成，中间由一些空格隔开
- `s` 表示一个 **有效表达式**
- 表达式中的所有整数都是非负整数，且在范围 `[0, 231 - 1]` 内
- 题目数据保证答案是一个 **32-bit 整数**

<br></br>

<br></br>

# 解答

### 单栈解决

一个栈，遇到数字字符进行累加，遇到符号时 +、- 直接将累加的数压入栈，遇到乘除就必须立即计算，从栈 pop 一个数字与累加的数进行计算，然后累加数重置为0。
这个算法最重要、最核心的地方是每次遇到符号进行计算时，使用的符号是上次遇到的符号（进行保存），在最后栈内都是一个个经过乘除后的数，直接一个个累加即可。

如果是两个栈的做法，才需要新建一个运算符优先级的图，进行每次比较做出相应运算



#### 代码

```
/**
 * @param {string} s
 * @return {number}
 */
var calculate = function(s) {
    let i, len = s.length, stack = [], operation = '+', num = 0;
    for(i = 0; i < len; i++) {
        // 如果不是NaN，即是整数
        if(!isNaN(Number(s[i])) && s[i] !== ' ') {
            num = num * 10 + (s[i] - '0');
        }
        if(isNaN(Number(s[i])) || i === len - 1){
            switch(operation) {
                case '+': stack.push(num); break;
                case '-': stack.push(-num); break;
                case '*': stack.push(stack.pop() * num); break;
                case '/': stack.push(~~(stack.pop() / num)); break;
            }
            operation = s[i];
            num = 0;
        }
    }
    let result = 0;
    while(stack.length) {
        result += stack.pop();
    }
    return result;
};
```



#### 时间和空间复杂度

![image-20220218210434237](https://gitee.com/zjc13544361063/zjc-markdown-picture/raw/master/image-20220218210434237.png)

<br></br>

### 双栈

利用双栈的思路，一个 nums栈 放数字，一个 ops栈 放运算符

先用 replace 替换掉 空字符串 (也可以遍历的时候处理)

遍历字符串，分两种情况：
如果当前是数字，收集起来，直到遇到运算符，就将数字添加到 nums栈 里面
如果当前是运算符(有可能面临着计算)

如果此时 ops栈 里面没有符号，则不需要运算，直接将当前运算符添加进去即可
如果此时 ops栈 有符号，且当前运算符的优先级 > ops栈里面最后一个符号的优先级时，不需要计算，推入ops栈即可

反之，则需要计算，但不是清空ops栈，循环ops栈，只需要计算到当前运算符的优先级 > ops栈最后一个运算符时，就停止计算。(例如 ops栈 此时 ['+', '*'] ， 此时运算符是 / ，只需要计算到 * 即可， + 运算符优先级比 / 低，不计算)

计算的时候，取出 ops栈 尾的元素，接着取出 nums栈 尾的最后两个元素分运算符计算即可。
考虑边界情况，当遍历到最后一位的时候，也需要计算一次
最后返回 nums[0] 即可。



#### 代码

```
function calculate(s) {
  // 创造数学逻辑上的运算符优先级
  const map = {
    '-': 1,
    '+': 1,
    '/': 2,
    '*': 2,
  }
  let res = '';
  let nums = []; // 存放数字
  let ops = []; // 存放运算符
  s = s.replace(/\s/g, '')
  for (let i = 0; i < s.length; i++) {
    let cur = s[i];
    if (/^[0-9]$/.test(cur)) {
      res += cur;
      // 处理遍历最后的时候
      if (i === s.length - 1) {
        nums.push(parseInt(res, 10));
        res = '';
        while (ops.length > 0) {
          cal(ops, nums);
        }
      }
    } else {
      nums.push(parseInt(res, 10));
      res = '';
      if (ops.length > 0) {
        // 比较当前运算符的优先级和ops栈里面最后一个运算符优先级的大小，区分是计算值还是放入nums栈中
        if (map[cur] > map[ops[ops.length - 1]]) {
          // 当前优先级大的话，需要将操作符放入栈中
          ops.push(cur);
        } else {
          // 当前优先级与ops栈里面最后一个运算符优先级同级或者小的话，需要计算值
          // 每一次消耗ops栈，下一次计算的时候都要去比较
          // 当前运算符的优先级和ops栈里面最后一个运算符优先级的大小
          while (ops.length > 0 && map[cur] <= map[ops[ops.length - 1]]) {
            cal(ops, nums);
          }
          ops.push(cur);
        }
      } else {
        ops.push(cur);
      }
    }
  }
  return nums[0];
}

function cal(ops, nums) {
  let opera = ops.pop();
  let after = nums.pop();
  let pre = nums.pop();
  let res = '';
  switch (opera) {
    case '*':
      res = pre * after;
      break;
    case '/':
      res = Math.floor(pre / after);
      break;
    case '+':
      res = pre + after;
      break;
    case '-':
      res = pre - after;
      break;
  }
  nums.push(parseInt(res, 10));
}
```



#### 时间和空间复杂度

![image-20220218210303649](https://gitee.com/zjc13544361063/zjc-markdown-picture/raw/master/image-20220218210303649.png)