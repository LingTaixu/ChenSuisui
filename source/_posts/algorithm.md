---
layout: old
title: algorithm
date: 2022-03-25 15:35:29
tags: 旧
---

### 快速排序 quickSort

> [ 12,6,23,61,23,72 ]

#### 理论知识点

1. 寻找到数组的`中间项` (是中间项而不是中位数此处指代的是长度的中间) 如上数组 长度除以二并向上或向下取整 则可得到一个整数 (长度为奇数取整同理) Math.floor(Array.length / 2)
2. 得到中位数的索引（下标后）将其从数组中移出并得到该数字作为比较的值 将剩下的数组依次进行比较 比该数小的放在左边，比该数大的放在右边
3. 同理可得 放入之后如果左右数组长度大于1 那么继续重复此操作 此时称为递归 如果数组长度小于等于 1 那么不需要再去进行下面的操作 直接抛出当前array
4. 最后合并该数组即可得到排序完毕的值

``` javascript
const array = [25, 62, 1, 27, 74, 21, 65, 22, 3, 7, 102, 2]
const quickSort = function (array) {
  if (array.length <= 1) return array // 长度小于等于 1 的时候抛出
  const middleIndex = Math.floor(array.length / 2)
  const middleValue = array.splice(middleIndex, 1)[0]
  const leftArray = [],
    rightArray = [];
  for (let i = 0; i < array.length; i++) {
    const val = array[i]
    array[i] < middleValue ? leftArray.push(val) : rightArray.push(val)
  }
  return quickSort(leftArray).concat(middleValue, quickSort(rightArray))
}
```