# 排序

# 1. 快排

1. 在数据集之中，选择任意一个元素作为"基准"（pivot）。
2. 所有小于"基准"的元素，都移到"基准"的左边；所有大于"基准"的元素，都移到"基准"的右边。
3. 对"基准"左边和右边的两个子集，不断重复第一步和第二步，直到所有子集只剩下一个元素为止。

```javascript
var quick_sort = (function fn(sort_array){

    if (sort_array.length <= 1) return sort_array 

    var sort_array_length = sort_array.length,
        privot = 0,
        left_array = _.filter(sort_array, function(element, index){
            return ( element <= sort_array[ privot ] && index !== privot ) 
        }),
        right_array = _.filter(sort_array, function(element, index){
            return  ( element > sort_array[ privot ] && index !== privot ) 
        })

        left_array = fn(left_array)
        right_array = fn(right_array)

    return left_array.concat([ sort_array[ privot ] ], right_array)
})

console.log(quick_sort([0,3,1,2,2]))
console.log(quick_sort([2,4,3,4,6,3,2,5,6,2,3,6,5,4]))
```

# 2. 冒泡

两个for

每经过一次第一层循环,都能拿到一个当前最小值

```javascript
var bubble_sort = function(sort_array){
    var sort_array_length = sort_array.length,
        tmp

    for (var i =  0 ; i < sort_array_length ; i++) {
        for (var j =  i ; j < sort_array_length ; j++) {
            if ( sort_array[ i ] >  sort_array[ j ] ) {
                tmp = sort_array[ i ]
                sort_array[ i ] = sort_array[ j ]
                sort_array[ j ] = tmp
            }
        }
    }
    return sort_array
}
console.log(bubble_sort([0,3]))
console.log(bubble_sort([0,3,4]))
console.log(bubble_sort([0,3,1,2,2]))
console.log(bubble_sort([2,4,3,4,6,3,2,5,6,2,3,6,5,4]))
```

# 参考链接

# 3. 归并排序

1. 将数组分为两两一对的排序好的数组,floor(n/2)个数组
2. 将floor(n/2)合并为floor(n/4)个数组
3. 直到只剩一个数组

合并排序规则: 不断拿两个数组的第0个元素进行对比,小的进入合并后数组,当一个数组为空时,另一个数组剩余元素加入到合并数组的最后

```javascript
var _merge = function(left, right){
    var tmp = []
    while ( left.length !== 0 && right.length !==0 ){
        left[0] > right[0] ? tmp.push( right.shift() ) : tmp.push( left.shift() )
    }
    return tmp.concat(left, right)
}

Array.prototype.merge_sort = function(){
    if ( this.length < 2 ) return this
    var mid = parseInt(this.length / 2),
        left = this.slice( 0, mid ).merge_sort(),
        right = this.slice(mid).merge_sort()
    return _merge( left, right )
}

console.log([0,3,1].merge_sort())
console.log([0,3].merge_sort())
```

1. 快排: <http://www.ruanyifeng.com/blog/2011/04/quicksort_in_javascript.html>
2. 归并排序: https://zh.wikipedia.org/wiki/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F