---
title: js实现排序的几种方法
date: 2017-10-02 15:20:04
tags: 算法
---
稳定性:若待排序的序列中，存在多个具有相同关键字的记录，经过排序， 这些记录的相对次序保持不变，则称该算法是稳定的；若经排序后，记录的相对 次序发生了改变，则称该算法是不稳定的。

稳定性的好处:排序算法如果是稳定的，那么从一个键上排序，然后再从另一个键上排序，第一个键排序的结果可以为第二个键排序所用。基数排序就是这样，先按低位排序，逐次按高位排序，低位相同的元素其顺序再高位也相同时是不会改变的。另外，如果排序算法稳定，可以避免多余的比较；

<!-- more -->

不稳定：
选择排序（selection sort）— O(n2)
快速排序（quicksort）— O(nlogn) 平均时间, O(n2) 最坏情况; 对于大的、乱序串列一般认为是最快的已知排序
堆排序 （heapsort）— O(nlogn)
选择排序（selection sort）— O(n2)
希尔排序 （shell sort）— O(nlogn)
基数排序（radix sort）— O(n·k); 需要 O(n) 额外存储空间 （K为特征个数）
稳定：
插入排序（insertion sort）— O(n2)
冒泡排序（bubble sort） — O(n2)
归并排序 （merge sort）— O(n log n); 需要 O(n) 额外存储空间
二叉树排序（Binary tree sort） — O(nlogn); 需要 O(n) 额外存储空间
计数排序 (counting sort) — O(n+k); 需要 O(n+k) 额外存储空间，k为序列中Max-Min+1
桶排序 （bucket sort）— O(n); 需要 O(k) 额外存储空间
选择排序算法准则
每种排序算法都各有优缺点。因此，在实用时需根据不同情况适当选用，甚至可以将多种方法结合起来使用。影响排序的因素有很多，平均时间复杂度低的算法并不一定就是最优的。相反，有时平均时间复杂度高的算法可能更适合某些特殊情况。同时，选择算法时还得考虑它的可读性，以利于软件的维护,一般而言，需要考虑的因素有以下四点：

待排序的记录数目n的大小；
记录本身数据量的大小，也就是记录中除关键字外的其他信息量的大小
关键字的结构及其分布情况
对排序稳定性的要求
当n较大，则应采用时间复杂度为O(nlog2n)的排序方法：快速排序、堆排序或归并排序序。
快速排序：是目前基于比较的内部排序中被认为是最好的方法，当待排序的关键字是随机分布时，快速排序的平均时间最短；

堆排序：如果内存空间允许且要求稳定性的

归并排序：它有一定数量的数据移动，所以我们可能过与插入排序组合，先获得一定长度的序列，然后再合并，在效率上将有所提高。

当n较大，内存空间允许，且要求稳定性 推荐 归并排序
当n较小，可采用直接插入或直接选择排序。
直接插入排序：当元素分布有序，直接插入排序将大大减少比较次数和移动记录的次数。

直接选择排序 ：元素分布有序，如果不要求稳定性，选择直接选择排序

一般不使用或不直接使用传统的冒泡排序。
基数排序它是一种稳定的排序算法，但有一定的局限性
关键字可分解

记录的关键字位数较少，如果密集更好

如果是数字时，最好是无符号的，否则将增加相应的映射复杂度，可先将其正负分开排序。

 

 

排序算法可以分为内部排序和外部排序。内部排序是数据记录在内存中进行排序，外部排序是因排序的数据很大，一次不能够容纳全部的排序记录，在排序中需要访问外存。常见的内部排序算法有插入排序，选择排序，冒泡排序，归并排序，快速排序，堆排序。

算法一：插入排序

算法步骤：

1.将第一待排序序列第一个元素看做一个有序数列，把第二个元素到最后一个元素当做未排序序列。

2.从头到尾依次扫描未排序序列，将扫描到的每个元素插入有序序列适当位置中（如果待插入的元素与有序数列中的某个元素相等，则待插入元素插入到相等元素的后面）

例如：19 2 8 11 24 6

2 19

2 8 19

2 8 11 19

2 8 11 19 24

2 6 8 11 19 24

代码实现：

    var arr=[77,56,3,8,4];
    function compare(arr){
        var newArr=[];
        newArr.push(arr[0]);//77
        for(var i=1;i<arr.length;i++){
            newArr.push(arr[i]);//56---77 56
            for(var j=newArr.length-2;j>=0;j--){
                if(newArr[j+1]<newArr[j]){
                    var temp = newArr[j];
                    newArr[j] = newArr[j+1];
                    newArr[j+1] = temp;//56 77
                }
            }
        }
        console.log(newArr);
    }
    compare(arr);

算法二：冒泡排序

算法步骤：

1.比较相邻的元素，如果第一个比第二个大，就交换他们两个

2.对每一对相邻的元素作同样的工作，从开始第一对到结尾的最后一对，这步做完后，最后的元素会是最大的数

3.针对所有的元素重复以上步骤，除了最后一个

4.持续对每次越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较

代码示例：

    var a=[38,9,2,14,6];
    for(var i=0;i<a.length;i++){
        for(var j=0;j<a.length-1;j++){
            if(a[j+1]>a[j]){
                a[j]=a[j]+a[j+1];
                a[j+1]=a[j]-a[j+1];
                a[j]=a[j]-a[j+1];
            }
        }
        console.log(a[i]);
    }

算法三：快速排序

算法步骤：

1.从数列中挑出一个元素，称为基准

2.重新排序数列，所有元素比基准值小的摆放在基准前面，所有愿所有比基准值大的摆在基准的后面（相同的数可以到任意一边），在这个分区退出之后，该基准就处于数列的中间位置，这个称之为分区操作。

3.递归的把所有小于基准值元素的子数列和大于基准值元素的子数列排序

递归的最底部情形是数列的大小是0或1，也就是永远都已经被排序好了，虽然一直递归下去，但这个方法总会退出，因为在每次的迭代中，它至少把一个元素摆到它最后的位置去。

代码示例：

    function qSort(a,left,right){
        var mid = a[left];
        while(left<right){
            while(a[right]>mid&&left<right)right--;
            if(left<right)
                a[left++]=a[right];
            while(a[left]<mid&&left<right) left++;
            if(left<right)
                a[right--]=a[left];
        }
        a[left] = mid;
        return left;
    }
    function quickSort(a,left,right){
        if(left<right){
            var mid = qSort(a,left,right);
            quickSort(a,left,mid-1);
            quickSort(a,mid+1,right);
        }
    }
    var a=[25,79,44,11,36,42,8];
    quickSort(a,0, a.length-1);
    for(var i=0; i< a.length;i++){
        console.log(a[i]);
    }

 

算法四：堆排序

算法步骤：

1.创建一个堆

2.把堆首和堆尾互换

3.把堆的尺寸缩小1，并调用shift-down(0),目的是把新的数组顶端数据调整到相应的位置

4.重复步骤2，直到堆的尺寸为1

代码示例：

    function headAdjust(elements, pos, len){
        var swap = elements[pos];
        var child = pos * 2 + 1;
        while(child < len){
            if(child + 1 < len && elements[child] < elements[child + 1]){
                child += 1;
            }
            if(elements[pos] < elements[child]){
                elements[pos] = elements[child];
                pos = child;
                child = pos * 2 + 1;
            }
            else{
                break;
            }
            elements[pos] = swap;
        }
    }
    function buildHeap(elements){
        for(var i=elements.length/2; i>=0; i--){
            headAdjust(elements, i, elements.length);
        }
    }
    function sort(elements){
        buildHeap(elements);
        for(var i=elements.length-1; i>0; i--){
            var swap = elements[i];
            elements[i] = elements[0];
            elements[0] = swap;
            headAdjust(elements, 0, i);
        }
    }
    var elements = [33, 17, 25, 77, 42, 24, 59, 16, 1, 38];
    sort(elements);
    console.log(elements);

 

算法五：归并排序

算法步骤：

1.申请空间，使其大小为两个排序序列之和，该空间用来存放合并后的数列

2.设定两个指针，最初位置分别是两个已经排序序列的起始位置

3.比较两个指针所指向的元素，选择相对小的元素放到合并空间，并移动指针到下一位置

4.重复步骤3直到某一指针达到序列尾

5.将另一序列剩下的所有元素直接复制到合并序列尾

代码示例：

    function mergeSort(arr){
        if(arr.length<2){
            return arr;
        }
        var middle = Math.floor(arr.length/2);
        var left = arr.slice(0,middle);
        var right = arr.slice(middle,arr.length);
        return merge(mergeSort(left),mergeSort(right));
    }
    function merge(left,right){
        var newArr = [];
        while(left.length&&right.length){
            if(left[0] <= right[0]){
                newArr.push(left.shift());
            }else{
                newArr.push(right.shift());
            }
        }
        while(left.length){
            newArr.push(left.shift());
        }
        while(right.length){
            newArr.push(right.shift());
        }
        return newArr;
    }
    var arr = [14,88,41,6,45,21];
    console.log(mergeSort(arr));

 

算法六：选择排序

算法步骤：
1.首先在未排序序列中找到最大或最小元素，存放到序列起始位置

2.再从剩余序列中找到最大或最小元素放到已排序序列末尾

3.重复第二步，直到所有元素都排序完毕

代码示例：

    function choseSort(a){
        for(var j=0;j< a.length-1;j++){
            var index = j;
            for(var i=j;i< a.length;i++){
                if(a[i]<a[index]){
                    index = i;
                }
            }
            var temp = a[j];
            a[j] = a[index];
            a[index] = temp;
        }
        return a[j];
    }
    var a = [15,2,41,30,9,14];
    choseSort(a);
    for(var i=0;i< a.length;i++){
        console.log(a[i]);
    }
