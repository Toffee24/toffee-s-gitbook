## 找出数组 arr 中重复出现过的元素

示例1

## 输入

```
[1, 2, 4, 4, 3, 3, 1, 5, 3]
```

## 输出

```
[1, 3, 4]
```

```
function duplicates(arr) {
     //声明两个数组，a数组用来存放结果，b数组用来存放arr中每个元素的个数
     var a = [],b = [];
     //遍历arr，如果以arr中元素为下标的的b元素已存在，则该b元素加1，否则设置为1
     for(var i = 0; i < arr.length; i++){
         if(!b[arr[i]]){
             b[arr[i]] = 1;
             continue;
         }
         b[arr[i]]++;
     }
     //遍历b数组，将其中元素值大于1的元素下标存入a数组中
     for(var i = 0; i < b.length; i++){
         if(b[i] > 1){
             a.push(i);
         }
     }
     return a;
 }



function duplicates(arr) {
  return arr.sort().filter((_, i) =>
    arr[i] === arr[i + 1] && arr[i] !== arr[i - 1]
  );
}
```



