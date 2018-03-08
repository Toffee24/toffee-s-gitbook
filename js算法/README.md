计算给定数组 arr 中所有元素的总和

## 输入描述:

```
数组中的元素均为 Number 类型
```

示例1

## 输入

```
[ 1, 2, 3, 4 ]
```

## 输出

```
10

```

不考虑算法复杂度，用递归做：

| 12345678910 | `functionsum(arr) {varlen = arr.length;if(len == 0){return0;}elseif(len == 1){returnarr[0];}else{returnarr[0] + sum(arr.slice(1));}}` |
| :--- | :--- |


 常规循环：

| 1234567 | `functionsum(arr) {vars = 0;for(vari=arr.length-1; i>=0; i--) {s += arr[i];}returns;}` |
| :--- | :--- |


 函数式编程 map-reduce：

| 12345 | `functionsum(arr) {returnarr.reduce(function(prev, curr, idx, arr){returnprev + curr;});}` |
| :--- | :--- |


 forEach遍历：

| 12345678 | `functionsum(arr) {vars = 0;arr.forEach(function(val, idx, arr) {s += val;}, 0);returns;};` |
| :--- | :--- |


 eval：

| 123 | `functionsum(arr) {returneval(arr.join("+"));};` |
| :--- | :--- |




