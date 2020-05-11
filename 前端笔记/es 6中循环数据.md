# [ES6 使用forEach遍历数组](https://www.cnblogs.com/BaoyuYang/p/8682438.html)

{
"roles": [
{"name": "admin","funs": "1,2,3"},
{"name": "调度员","funs": "1,3"},
{"name": "司机","funs": "3"}
]
};

2、打印

role.GetList().roles.forEach(logArrayElements);

function logArrayElements(element, index, array) {
console.log("a[" + index + "] = " + element.name);
}

3、forEach解释：

## 语法

```
array.forEach(callback(currentValue, index, array){
    //do something
}, this)

array.forEach(callback[, thisArg])
```

### 参数

- `callback`

  为数组中每个元素执行的函数，该函数接收三个参数：

  currentValue(当前值)数组中正在处理的当前元素。index(索引)数组中正在处理的当前元素的索引。arrayforEach()方法正在操作的数组。

- `thisArg`可选

  可选参数。当执行回调 函数时`用作``this的`值(参考对象)。

- 

### 返回值

[`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined).

## 描述

```
forEach` 方法按升序为数组中含有效值的每一项执行一次`callback` 函数，那些已删除（使用`delete`方法等情况）或者未初始化的项将被跳过（但不包括那些值为 `undefined 的项）（例如在稀疏数组上）。
```

`callback` 函数会被依次传入三个参数：

- **数组当前项的值**
- **数组当前项的索引**
- **数组对象本身**

如果`给forEach传递了thisArg`参数，当调用时，它将被传给`callback` 函数，作为它的this值。否则，将会传入 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined) 作为它的this值。callback函数最终可观察到this值，这取决于 [函数观察到`this`的常用规则](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)。



数组方法 ：map ,filter,find









