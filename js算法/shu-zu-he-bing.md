## 合并数组 arr1 和数组 arr2。不要直接修改数组 arr，结果返回新的数组

示例1

## 输入

```
[1, 2, 3, 4], ['a', 'b', 'c', 1]
```

## 输出

```
[1, 2, 3, 4, 'a', 'b', 'c', 1]
```



```
//利用concat
function concat(arr1, arr2) {
    return arr1.concat(arr2);
}
//利用slice+push.apply
function concat(arr1, arr2) {
    var newArr=arr1.slice(0);
    [].push.apply(newArr, arr2);
    return newArr;
}
//利用slice+push
function concat(arr1, arr2) {
    var newArr=arr1.slice(0);
    for(var i=0;i<arr2.length;i++){
        newArr.push(arr2[i]);
    }
    return newArr;
}
//普通的迭代拷贝
function concat(arr1, arr2) {
    var newArr=[];
    for(var i=0;i<arr1.length;i++){
        newArr.push(arr1[i]);
    }
    for(var j=0;j<arr2.length;j++){
        newArr.push(arr2[j]);
    }
    return newArr;
}
```



