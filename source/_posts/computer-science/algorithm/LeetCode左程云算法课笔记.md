---
    
layout: post
title: LeetCode左程云算法课笔记
tags: 算法
categories:
- [计算机科学, 算法]
---

# 位运算

## 异或运算符^

### ^运算符性质

* \^可理解为不进位相加

* 0\^N == N, N\^N == 0

* 异或运算满足交换律和结合律，a\^b = b^a, a\^b\^c = a\^(b\^c)

* 根据异或相同为0，不同为1不能很好理解交换律和结合律，但是^理解为不进位相加就很好理解了，某一位上的结果只和1的个数有关，和顺序无关

  ![](https://gitee.com/nobody_heard_of_it/pic-md1/raw/master/image/20211212133439.png)
### 使用异或运算符交换两个数

```java
/**
* arr[i] = a, arr[j] = b
*/
public static void swap(int[] arr, int i, int j){
    arr[i] = arr[i] ^ arr[j];  //arr[i] = a ^ b
    arr[j] = arr[i] ^ arr[j];  //arr[j] = a ^ b ^ b = a
    arr[i] = arr[i] ^ arr[j];  //arr[i] = a ^ b ^ a = b
} 
```

* 不用申请额外的空间
* **使用前提**：i != j, 即要交换的两数在内存中地址不能一样，否则会将该地址的值抹为0

### 提取出最右的1

```java
/**
*假设最右侧1为第i位，首先~num将0~i-1位的所有数都取反，i位之后的数都置为0，加1后i位变为1，0~i-1位均为0，并进位到i使i位上的数字变为1，i位之后的数字仍未原数字的取反，此时再与原数字求与，则除了i位上的数字为1，其它位均为0
*/
int rightOne = num & (~num+1)
```



### 一个数组中有一种数出现了奇数次，其它数都出现了偶数次，怎么找到这一个数

```java
//时间复杂度O(N),空间复杂度O(1)
public static void printOddTimesNum1(int arr[]){
    int eor = 0;
    for(int cur:arr){
        eor ^= cur;
    }
    System.out.println(eor)
}
```

### 一个数组中有两种数出现了奇数次，其他数都出现了偶数次，怎么找到这两个数

```java
//时间复杂度O(N),空间复杂度O(1)
public static void pringOddTimesNum2(int arr[]){
    int eor = 0;
    for(int cur:arr){
        eor ^= arr;
    }
    // a!=b
    //eor = a ^ b
    //eor != 0
    //eor必然有一个位置上是1，其中该位置上a,b中必有1个为0，一个为1，假设该位上a为1，b为0
    int rightOne = eor & (~eor+1);  //提取出最右的1
    int onlyOne = 0; //eor' 
    //所有的数可以分为rightOne位上为1和不为1的两类，每类中的数除了a或b外全都出现了偶次
    for(int curNum:arr){
        if(rightOne & curNum == 0){  // 与数进行与运算，找出所有的该位上为0的数
            // 与所有的该位上为0的数异或，最终onlyOne = a ^ b ^ b = a
            onlyOne ^= curNum;
        }
        //if(rightOne & curNum == rightNum){  // 与数进行与运算，找出所有的该位上为1的数
        // 与所有的该位上为1的数异或，最终onlyOne = a ^ b ^ a = b
        //    onlyOne ^= curNum;
        //}
    }
    System.out.println(onlyOne+" "+(eor ^ onlyNum))  //eor ^ onlyNum = a ^ b ^ a = b
}
```

# 排序算法

## 基于比较的排序算法

### 选择排序

```java
public class Code01_SelectionSort {
	public static void selectionSort(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
        //每次选出最小的数
		for (int i = 0; i < arr.length - 1; i++) {
			int minIndex = i;
			for (int j = i + 1; j < arr.length; j++) {
				minIndex = arr[j] < arr[minIndex] ? j : minIndex;
			}
			swap(arr, i, minIndex);
		}
	}

	public static void swap(int[] arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}
}
```

### 冒泡排序

```java
public class Code02_BubbleSort {
	public static void bubbleSort(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
        //相邻的两个数比较，大的放在右边
		for (int e = arr.length - 1; e > 0; e--) {
			for (int i = 0; i < e; i++) {
				if (arr[i] > arr[i + 1]) {
					swap(arr, i, i + 1);
				}
			}
		}
	}

	public static void swap(int[] arr, int i, int j) {
		arr[i] = arr[i] ^ arr[j];
		arr[j] = arr[i] ^ arr[j];
		arr[i] = arr[i] ^ arr[j];
	}
}
```

### 插入排序

时间复杂度O(N^2)，额外空间复杂度O(1)，算法流程按照最差情况来估计时间复杂度

```java
public class Code3_InsertionSort{
    public static void insertionSOrt(int[] arr){
        if(arr == null || arr.length<2){
            return;
        }
        //0~0有序
        //0~i想有序
        for(int i=1;i < arr.length; i++){  //0~i范围有序
            for(int j = i-1; j>=0 && arr[j]>arr[j+1]; j--){
                swap(arr, j, j+1);
            }
        }
    }
    
    //i和j是一个位置会报错，使用异或操作交换数字必须保证两数地址不同
    public static void swap(int[] arr, int i, int j){ 
        arr[i] = arr[i] ^ arr[j];
        arr[j] = arr[i] ^ arr[j];
        arr[i] = arr[i] ^ arr[j];
    }
}
```

### 归并排序

T(N) = 2T(N/2)+O(N)

log <sub>a</sub>b = d,时间 复杂度为O(N^d*log(b,a))=O(N)

空间复杂度为O(N) 

#### 归并排序代码实现

```java
public class MergeSort{
	public static void mergeSort(int[] arr){
        if(arr==null||arr.length<2){
            return;
        }
        process(arr, 0, arr.length-1);
	}

    public static void process(int[] arr, int L,int R){
        if(L==R){
            return;
        }
        int mid = L+((R-L)>>1);
        process(arr,L,mid);
        process(arr,mid+1,R);
        merge(arr,L,mid,R);
    }

    public static void merge(int[] arr, int L, int mid, int R){
        int[] help = new int[R-L+1];
        int i = 0;
        int p1 = L;
        int p2 = mid+1;
        while(p1<=mid&&p2<=R){
            help[i++] = arr[p1]<=arr[p2]?arr[p1++]:arr[p2++];
        }
        while(p1<=L){
            help[i++] = arr[p1++];
        }
        while(p2<=R){
            help[i++] = arr[p2++];
        }
        for(i=0; i<help.length; i++){
            arr[L+i] = help[i];
        }
    }
    public static void main(String[] args) {
        int[] arr = {1, 3, 2 ,5,8,9,10,6};
        mergeSort(arr);
        for (int i : arr) {
            System.out.println(i);
        }
    }
}
```

#### 小和问题

在一个数组中，每一个数左边比当前数小的数累加起来，叫做这个数组 的小和。求一个数组 的小和。 例子:[1,3,4,2,5] 1左边比1小的数，没有; 3左边比3小的数，1; 4左 边比4小的数，1、3; 2左边比2小的数，1; 5左边比5小的数，1、3、4、 2; 所以小和为1+1+3+1+1+3+4+2=16

**解题思路**：求解某数的右边比有多少个比该数大的数，对该数数值*个数求和即为所求结果

**为什么要排序**：排序后计算个数只需下标相减即可求出个数

**大概流程**：左侧数组的小和加上右侧数组的小和再加上合并过程中的数组的小和

```java
public class SMallNum {
    public static int smallNum(int[] arr){
        if(arr==null || arr.length<2){
            return 0;
        }
        return mergeSort(arr, 0, arr.length-1);
    }

    /**
     * 
     */
    public static int mergeSort(int[] arr, int l, int r){
        if(l==r){
            return 0;
        }
        int mid = l+((r-l)>>1);
        return mergeSort(arr, l, mid)  //左侧数组的小和
            +mergeSort(arr, mid+1, r)  //右侧数组的小和
            +merge(arr, l, mid, r);    //合并过程中的小和
    }

    /**
     * 
     */
    public static int merge(int[] arr, int l, int mid, int r){
        int[] help = new int[r-l+1];
        int i = 0;
        int p1 = l;
        int p2 = mid+1;
        int res = 0;
        //遍历左侧数组和右侧数组
        //当左侧的值小于右侧的值时，右侧下标相减求出个数*左侧数的值
        while(p1<=mid&&p2<=r){
            res += arr[p1]<arr[p2]?arr[p1]*(r-p2+1):0;  
            help[i++] = arr[p1]<arr[p2]?arr[p1++]:arr[p2++];
        }
        while(p1<=mid){
            help[i++] = arr[p1++];
        }
        while(p2<=r){
            help[i++] = arr[p2++];
        }
        for(i=0; i<help.length;i++){
            arr[l+i] = help[i];
        }
        return res;
    }
    public static void main(String[] args) {
        int[] arr = {1, 3, 4, 2, 5};
        int res = smallNum(arr);
        System.out.print("排序后的数组为:");
        for (int i : arr) {
            System.out.print(i+"\t");
        }
        System.out.println("\n"+"小和为："+res);
    }
}

```

![](https://gitee.com/nobody_heard_of_it/pic-md1/raw/master/image/20211212211300.png)

#### 数组中的逆序对

**数组中的两个数，若前面的一个数大于后面的一个数，那么这两个数组成一个逆序对。输入一个数组，返回逆序对的个数。**

**解题思路**：大题思路与求小和问题差不多，不过要将数组从大到小排列

**为什么要排序**：排序后计算个数只需下标相减即可求出个数

```java
public class InversionPair {
    public static int inversionPair(int[] arr){
        if(arr==null || arr.length<2){
            return 0;
        }
        return mergeSort(arr, 0, arr.length-1);
    }

    /**
     * 
     */
    public static int mergeSort(int[] arr, int l, int r){
        if(l==r){
            return 0;
        }
        int mid = l+((r-l)>>1);
        return mergeSort(arr, l, mid)+mergeSort(arr, mid+1, r)+merge(arr, l, mid, r);
    }

    /**
     * 
     */
    public static int merge(int[] arr, int l, int mid, int r){
        int[] help = new int[r-l+1];
        int i = 0;
        int p1 = l;
        int p2 = mid+1;
        int res = 0;
        while(p1<=mid&&p2<=r){
            
            //打印所有逆序对
            if(arr[p1]>arr[p2]){
                int j = p2;
                while(j<=r){
                    System.out.println(arr[p1]+" "+arr[j++]);
                }
            }
            res += arr[p1]>arr[p2]?(r-p2+1):0;
            // if(arr[p1]>arr[p2]){
            //     int j = p2
            //     while(j<r){
            //         System.out.println(arr[p1]+" "+arr[j++]+" "+res);
            //     }
            // }
            help[i++] = arr[p1]>arr[p2]?arr[p1++]:arr[p2++];
        }
        while(p1<=mid){
            help[i++] = arr[p1++];
        }
        while(p2<=r){
            help[i++] = arr[p2++];
        }
        for(i=0; i<help.length;i++){
            arr[l+i] = help[i];
        }
        return res;
    }

    public static void main(String[] args) {
        int[] arr = {1, 3, 4, 2,5,0};
        int res = inversionPair(arr);
        System.out.print("排序后的数组为:");
        for (int i : arr) {
            System.out.print(i+"\t");
        }
        System.out.println("\n"+"数组中逆序对的个数为："+res);
    }
}

```

![](https://gitee.com/nobody_heard_of_it/pic-md1/raw/master/image/20211213110617.png)

### 快速排序

#### 荷兰国旗问题

##### 问题一

给定一个数组arr，和一个数num，请把小于等于num的数放在数 组的左边，大于num的 数放在数组的右边。要求额外空间复杂度O(1)，时间复杂度O(N)

```java
public static int[] partition(int[] arr, int L, int R) {
    int less = L-1;   //左边界
    while(L<more){     // L表示当前数的位置
        if(arr[L]<=arr[R]){
            //当前坐标下的值小于等于num,则当前的数与左边界下一个坐标的数相交换，左边界右移一个，L++，对比下一个数
            swap(arr,++less,L++);  //
        }
        else{
            //当前坐标下的值大于num,L++查找下一个数，左边界不变
            L++;
        }
    }
}
```

##### 问题二

给定一个数组arr，和一个数num，请把小于等于num的数放在数 组的左边，大于num的 数放在数组的右边。要求额外空间复杂度O(1)，时间复杂度O(N)

```java
public static int[] partition(int[] arr, int L, int R) {
    int less = L-1;   //左边界
    int more = R;     //右边界
    while(L<more){     // L表示当前数的位置
        if(arr[L]<arr[R]){
            //当前坐标下的值小于num,则当前的数与左边界下一个坐标的数相交换，左边界右移一个，L++，对比下一个数
            swap(arr,++less,L++);  //
        }
        else if(arr[L]>arr[R]){
            //当前坐标下的值小于num,则当前的数与右边界前一个坐标的数相交换，右边界左移一个
            //L不变，因为右边界前一个坐标的数还未与num相比，需比较，因此L不变
            swap(arr, --more, L);   
        }
        else{
                //当前坐标下的值等于num,L++查找下一个数，左右边界不变
            L++;
        }
    }

    //当L==More时, More边界左边全是小于等于num的,将num与右边界上的数交换则数组排序完成
    //与左边街上的数交换也可
    swap(arr, R, more);
    return new int[] {less,more};
}
```

#### 快速排序代码实现

**不改进的快速排序**

1. 把数组范围中的最后一个数作为划分值，然后把数组通过荷兰国旗问题分成三个部 分： 左侧<划分值、中间==划分值、右侧>划分值
2. 对左侧范围和右侧范围，递归执行 

**分析**

1. 划分值越靠近两侧，复杂度越高；划分值越靠近中间，复杂度越低 
2. 可以轻而易举的举出最差的例子，所以不改进的快速排序时间复杂度为O(N^2)

**随机快速排序（改进的快速排序）**

1. 在数组范围中，等概率随机选一个数作为划分值，然后把数组通过荷兰国旗问题分 成三个部分： 左侧<划分值、中间==划分值、右侧>划分值 
2. 对左侧范围和右侧范围，递归执行 
3. 时间复杂度为O(N*logN)
4. 空间复杂度O(logN) 

```java
import java.util.Arrays;

public class QuickSOrt {
    public static void quickSort(int[] arr){
        if(arr == null || arr.length<2){
            return;
        }
        quickSort(arr, 0, arr.length-1);
    }

    //arr[l……R]排好序
    public static void quickSort(int[] arr, int L, int R) {
        if(L<R){
            //将一个随机下标的数置为num
            //生成的随即下标应随R,L的长度动态改变而且要记得加上L
            //错误写法swap(arr, (int)Math.random()*(arr.length-1),R);
            //在数组范围中，等概率随机选一个数作为划分值
            swap(arr, (int)Math.random()*((R-L)+1)+L,R);
            int[] p = partition(arr, L, R);
            quickSort(arr, L, p[0]);
            quickSort(arr, p[1]+1, R);
        }
        return;
    }

    public static int[] partition(int[] arr, int L, int R) {
        int less = L-1;   //左边界
        int more = R;     //右边界
        while(L<more){     // L表示当前数的位置
            if(arr[L]<arr[R]){
                //当前坐标下的值小于num,则当前的数与左边界下一个坐标的数相交换，左边界右移一个，L++，对比下一个数
                swap(arr,++less,L++);  //
            }
            else if(arr[L]>arr[R]){
                //当前坐标下的值小于num,则当前的数与右边界前一个坐标的数相交换，右边界左移一个
                //L不变，因为右边界前一个坐标的数还未与num相比，需比较，因此L不变
                swap(arr, --more, L);   
            }
            else{
                 //当前坐标下的值等于num,L++查找下一个数，左右边界不变
                L++;
            }
        }

        //当L==More时, More边界左边全是小于等于num的,将num与右边界上的数交换则数组排序完成
        //与左边街上的数交换也可
        swap(arr, R, more);
        return new int[] {less,more};
    }

    public static void swap(int[] arr, int L, int R){
        int temp = arr[L];
        arr[L] = arr[R];
        arr[R] = temp;
    }

    //不能用异或交换数字，因为L,R不能确定不同，最后可能出现值被赋为0的情况
    // public static void swap(int[] arr, int L, int R){
    //     arr[L] = arr[L] ^ arr[R];
    //     arr[R] = arr[L] ^ arr[R];
    //     arr[L] = arr[L] ^ arr[R];
    // }

    // for test
	public static void comparator(int[] arr) {
		Arrays.sort(arr);
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

    public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue); 
			// int[] arr1 = {1,4,6,8};
			int[] arr2 = copyArray(arr1);
			quickSort(arr1);
			comparator(arr2);
			if (!isEqual(arr1, arr2)) {
				succeed = false;
				printArray(arr1);
				printArray(arr2);
				break;
			}
		}
		System.out.println(succeed ? "耶，成功了!" : "不!怎么错了");

		int[] arr = generateRandomArray(maxSize, maxValue);
		printArray(arr);
		quickSort(arr);
		printArray(arr);

	}

}

```

### 堆排序

时间复杂度O(NLogN)

空间复杂度O（1）

- Heap是一种数据结构具有以下的特点：
  1）**完全二叉树**；
  2）heap中存储的值是**偏序**；

- **Min-heap**: 父节点的值小于或等于子节点的值；
  **Max-heap**: 父节点的值大于或等于子节点的值；

   ![](https://gitee.com/nobody_heard_of_it/pic-md1/raw/master/image/20211213195011.png)

* **堆的存储：**

一般都用数组来表示堆，i结点的父结点下标就为(i–1)/2。它的左右子结点下标分别为2 * i + 1和2 * i + 2。如第0个结点左右子结点下标分别为1和2。

* 优先级队列结构，就是堆结构

#### 向堆中添加新元素（以大根堆为例）

向堆中添加新元素首先将其添加至堆的末尾，然后再与其父节点比较，如果大于其父节点，将其与父节点相交换，再与新的父节点比较，直至小于新的父节点或者已为根节点。

从上到下的方法

```java
//不断遍历数组，即添加新元素
for (int i = 0; i < arr.length; i++) {
    heapInsert(arr, i);
}

// 确保堆为大堆,某个数现在在index位置上，往上继续移动
public static void heapInsert(int[] arr, int index) {
    // index不再比父大或者index到了0位置即根节点
    while (arr[index] > arr[(index - 1) / 2]) {
        swap(arr, index, (index - 1) / 2);
        index = (index - 1) / 2;
    }
}

```

从下到上的方法

```java
for (int i = arr.length-1; i >= 0; i--) {
    heapify(arr, i, arr.length);
}
```



#### 删除的堆第0个数据，即根节点

将根节点的数据与最后一个节点的数据进行交换，并将堆的长度减一，同时将新的根节点不断与他的较大的儿子节点相比较（若有儿子节点的话），若小于儿子节点，则将其与儿子节点交换，交换后再与其较大的儿子节点相比较直至大于所有的儿子节点或者没有儿子节点。

```java
// 去除了原有的最大值后使剩下的堆仍为大堆,index为当前节点的位置
public static void heapify(int[] arr, int index, int heapSize) {
    int left = 2 * index + 1;
    //判断该节点有没有儿子节点
    while (left < heapSize) {
        int right = left + 1;
        // 将左儿子与右儿子比较，将index与大的儿子节点比较
        //判断有没有右儿子节点，没有直接与左儿子节点比较
        int larger = right < heapSize && arr[left] < arr[right] ? right : left;
        int largest = arr[index] > arr[larger] ? index : larger;
        if (index == largest) {
            break;
        }
        //将该节点与其较大的儿子节点相比较
        swap(arr, index, largest);
        index = largest;
        //新的儿子节点
        left = 2 * index + 1;
    }
}
```

#### 堆排序代码实现

要实现堆排序，首先将所给数组变成一个大根堆，然后再不断将根节点放到堆的长度的数组末尾（每取出一个最大值，堆的长度减一）就可以了，最终得到的结果为从小到大排列的数组（若为小根堆，则得到的是从大到小排列的数组）。

```java
import java.util.Arrays;

public class HeapSort {

    public static void heapSort(int[] arr) {
        if (arr == null || arr.length < 2) {
            return;
        }
        //将数组变为大根堆
        for (int i = 0; i < arr.length; i++) {
            heapInsert(arr, i);
        }

        int heapSize = arr.length;
        // 去除最大值，将其放在数组最末尾
        swap(arr, 0, --heapSize);
        // 每次将最大的数存放在数组的最末尾，因此使用大堆最后得到的排序结果为从小到大
        while (heapSize > 0) {
            heapify(arr, 0, heapSize);
            swap(arr, 0, --heapSize);
        }
    }

    // 确保堆为大堆,某个数现在在index位置上，往上继续移动
    public static void heapInsert(int[] arr, int index) {
        // index不再比父大或者index到了0位置即根节点
        while (arr[index] > arr[(index - 1) / 2]) {
            swap(arr, index, (index - 1) / 2);
            index = (index - 1) / 2;
        }
    }

    // 去除了原有的最大值后使剩下的堆仍为大堆,index为当前节点的位置
    public static void heapify(int[] arr, int index, int heapSize) {
        // 首先与左儿子比较
        int left = 2 * index + 1;
        while (left < heapSize) {
            int right = left + 1;
            // 将左儿子与右儿子比较，将index与大的交换
            int larger = right < heapSize && arr[left] < arr[right] ? right : left;
            int largest = arr[index] > arr[larger] ? index : larger;
            if (index == largest) {
                break;
            }
            swap(arr, index, largest);
            index = largest;
            left = 2 * index + 1;
        }
    }

    public static void swap(int[] arr, int L, int R) {
        int temp = arr[L];
        arr[L] = arr[R];
        arr[R] = temp;

    }

    public static int[] generateRandomArray(int maxSize, int maxValue) {
        int[] arr = new int[(int)((maxSize+1)*Math.random())];
        for(int i =0; i<arr.length;i++){
            arr[i] = (int)((maxValue+1)*Math.random())-(int)((maxValue+1)*Math.random());
        }
        return arr;
    }

    public static int[] copyArray(int[] arr) {
        if(arr==null){
            return null;
        }
        int[] res = new int[arr.length];
        for (int i = 0; i < res.length; i++) {
            res[i] = arr[i];
        }
        return res;
    }

    public static void comparator(int[] arr) {
        Arrays.sort(arr);
    }

    public static boolean isEqual(int[] arr1, int[] arr2) {
        if (arr1 == null && arr2 == null) {
            return true;
        }
        if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
            return false;
        }
        if (arr1.length != arr2.length) {
            return false;
        }
        for (int i = 0; i < arr1.length; i++) {
            if (arr1[i] != arr2[i]) {
                return false;
            }
        }
        return true;
    }

    public static void printArray(int[] arr) {
        System.out.println("数组为");
        for (int i : arr) {
            System.out.print(i + "  ");
        }
        System.out.println();
    }

    public static void main(String[] args) {
        int testTime = 500000;
        int maxSize = 100;
        int maxValue = 100;
        boolean succeed = true;
        for (int i = 0; i < testTime; i++) {
            int[] arr1 = generateRandomArray(maxSize, maxValue);
            int[] arr2 = copyArray(arr1);
            heapSort(arr1);
            comparator(arr2);
            if (!isEqual(arr1, arr2)) {
                succeed = false;
                break;
            }
        }
        int[] arr1 = generateRandomArray(maxSize, maxValue);
        int[] arr2 = copyArray(arr1);
        heapSort(arr1);
        comparator(arr2);
        System.out.println(succeed ? "耶！成功了" : "不！失败了");
        printArray(arr1);
        // int[] arr = {5,3,6,8};
    }
}

```

#### 堆排序扩展题目 

已知一个**几乎有序**的数组，几乎有序是指，如果把数组排好顺序的话，每个元素移动的距离可以不超过k，并且k相对于数组来说比较小。请选择一个合适的 排序算法针对这个数据进行排序。

**思路：**构建一个大小为k+1的小根堆，因为数组几乎有序，因此k+1大小的堆内的最小值一定是后续所有的数里最小的，因此只需每次都弹出堆的根节点即最小值并加入下一个值。直到所有的数都遍历完。

```java
public static void sortArrayDistanceLessK(int[] arr, k){
    //优先级队列就是堆结构
    //现成的堆结构，默认为小堆
    PriorityQueue<Integer> heap =  new PriorityQueue<>();
    //将k+1个数加入堆中
    int index = 0;
    for(;index < Math.min(arr.length, k+1); index++){
        heap.add(arr[index]);
    }
    while(index<arr.length){
        arr[index-k] = heap.poll();
        heap.add(arr[index++]);
    }
    while(heap!=null){
        arr[index-k] = heap.poll();
        index++;
    }
}
```

## 不基于比较的排序

### 计数排序

假设输入的线性表L的长度为n，L=L1,L2,..,Ln；线性表的元素属于有限偏序集S，|S|=k且k=O(n)，S={S1,S2,..Sk}；则计数排序可以描述如下：

1、扫描整个集合S，对每一个Si∈S，找到在线性表L中小于等于Si的元素的个数T(Si)；

2、扫描整个线性表L，对L中的每一个元素Li，将Li放在输出线性表的第T(Li)个位置上，并将T(Li)减1。

### 基数排序（radix sort）

#### 思路：

遍历所有位，并将当前位上的数字按照计数排列，遍历一次后更新一次数组，直到遍历完成

#### 大概流程：

##### 辅助数组的作用

* count数组，用于统计当前位上各数字有多少个

  count[0],当前位上等于0的数字的个数
  count[1],当前位上等于1的数字的个数

* bucket数组

  用于暂存按照当前位上数字大小排好序的数组

##### 计算出数组最大数有多少位

```java
public static int maxBits(int[] arr) {
    int maxValue = Integer.MIN_VALUE;
    // 求数组中最大的数
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] > maxValue) {
            maxValue = arr[i];
        }
    }
    int bits = 0;
    while (maxValue != 0) {
        bits++;
        maxValue = maxValue / 10;
    }
    return bits;
}
```

##### 遍历各位数

```java
// 从低次排到高次，大的放后面
for (i = 1; i <= digit; i++) {
    ...
}
```

##### 对当前位上的数进行计数排列

```java
int[] count = new int[radix];

for (int j = L; j <= R; j++) {
    // 获取arr[j] 第 i位上的数字v,count[v]++
    int v = getDigit(arr[j], i);
    count[v]++;
}

//获取digit位上的数字
public static int getDigit(int num, int digit) {
    for (int i = 0; i < digit - 1; i++) {
        num = num / 10;
    }
    return num % 10;
}
```

更新count桶

```java
// 统计当前位上小于i+1的共有多少个数
// 若当前位上为3,修改后的count[3]为5，则说明当前位上小于4的数有5个
// 便于后期往bucket中存入数据
for (int j = 1; j < radix; j++) {
    count[j] = count[j] + count[j - 1];
}
```

存入bucket中并更新arr

```java
for (int j = R; j >= L; j--) {
    // 获取i位上的数字
    int v = getDigit(arr[j], i);
    // 将其放入bucket桶中
    bucket[count[v] - 1] = arr[j];
    // 存入一个则容量减一
    count[v]--;
}
for (int j = L; j <= R; j++) {
    arr[j] = bucket[j];
}
```

![](https://gitee.com/nobody_heard_of_it/pic-md1/raw/master/image/20211214153736.png)

#### 需要注意的地方

**将数据存入bucket中时一定要记得从右往左读取arr中的数据,否则就会出现下面的情况，低位数据从大到小排列**

![](https://gitee.com/nobody_heard_of_it/pic-md1/raw/master/image/20211214152821.png)

因此上图中不应先读arr[0],应先读啊arr[4]

```java
// 将数组放入bucket桶中
//一定要从右往左读取arr的数据
//因为存入bucket时i相同的话是从后往前存的，因此要保证先读取低位较大的数据
for (int j = R; j >= L; j--) {   //关键
    // 获取i位上的数字
    int v = getDigit(arr[j], i);
    // 将其放入bucket桶中
    bucket[count[v] - 1] = arr[j];
    // 存入一个则容量减一
    count[v]--;
}
for (int j = L; j <= R; j++) {
    arr[j] = bucket[j];
}
```

#### 基数排序完整代码实现

```java
import java.util.Arrays;

public class RadixSort {
    public static void radixSort(int[] arr) {
        if (arr == null || arr.length < 2) {
            return;
        }
        // 计算最大的数的位数
        int digit = maxBits(arr);

        radixSort(arr, 0, arr.length - 1, digit);
    }

    public static void radixSort(int[] arr, int L, int R, int digit) {
        final int radix = 10;
        // 用于存放按当前i位上的数字排好序的数组
        int[] bucket = new int[R - L + 1];
        int i = 0;

        // 从低次排到高次，大的放后面
        for (i = 1; i <= digit; i++) {
            // 辅助桶，用于统计当前位上数字等于i有多少个数字
            // count[0],当前位上等于0的数字的个数
            // count[1],当前位上等于1的数字的个数
            int[] count = new int[radix];

            for (int j = L; j <= R; j++) {
                // 获取arr[j] 第 i位上的数字v,count[v]++
                int v = getDigit(arr[j], i);
                count[v]++;
            }

            // 统计当前位上小于i+1的共有多少个数
            // 若当前位上为3,修改后的count[3]为5，则说明当前位上小于4的数有5个
            // 便于后期往bucket中存入数据
            for (int j = 1; j < radix; j++) {
                count[j] = count[j] + count[j - 1];
            }
            // 将数组放入bucket桶中
            //一定要从右往左读取arr的数据
            //因为存入bucket时i相同的话是从后往前存的，因此要保证先读取低位较大的数据
            for (int j = R; j >= L; j--) {
                // 获取i位上的数字
                int v = getDigit(arr[j], i);
                // 将其放入bucket桶中
                bucket[count[v] - 1] = arr[j];
                // 存入一个则容量减一
                count[v]--;
            }
            for (int j = L; j <= R; j++) {
                arr[j] = bucket[j];
            }
        }
        return;
    }

    public static int maxBits(int[] arr) {
        int maxValue = Integer.MIN_VALUE;
        // 求数组中最大的数
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] > maxValue) {
                maxValue = arr[i];
            }
        }
        int digit = 0;
        while (maxValue != 0) {
            digit++;
            maxValue = maxValue / 10;
        }
        return digit;
    }

    public static int getDigit(int num, int digit) {
        for (int i = 0; i < digit - 1; i++) {
            num = num / 10;
        }
        return num % 10;
    }

    public static void printArray(int[] arr) {
        for (int i : arr) {
            System.out.printf(i + "\t");
        }
        System.out.println();
    }

    public static int[] generateRandomArray(int maxSize, int maxValue) {
        int[] arr = new int[(int) (Math.random() * maxSize)];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = (int) (Math.random() * maxValue);
        }
        return arr;
    }

    public static int[] copyArray(int[] arr) {
        int[] arr2 = new int[arr.length];
        for (int i = 0; i < arr.length; i++) {
            arr2[i] = arr[i];
        }
        return arr2;
    }

    public static boolean isEqual(int[] arr1, int[] arr2) {
        if ((arr1 == null && arr2 != null) || (arr2 == null && arr1 != null)) {
            return false;
        }
        if (arr1 == null && arr2 == null) {
            return true;
        }

        if (arr1.length != arr2.length) {
            return false;
        }
        for (int i = 0; i < arr2.length; i++) {
            if (arr1[i] != arr2[i]) {
                return false;
            }
        }

        return true;
    }

    public static void comparator(int[] arr) {
        Arrays.sort(arr);
    }

    public static void main(String[] args) {
        int testTime = 500000;
        int maxValue = 100;
        int maxSize = 100;
        boolean succeed = true;
        for (int i = 0; i < testTime; i++) {
            int[] arr1 = generateRandomArray(maxSize, maxValue);
            int[] arr2 = copyArray(arr1);
            radixSort(arr1);
            comparator(arr2);
            if (!isEqual(arr1, arr2)) {
                succeed = false;
                printArray(arr1);
                printArray(arr2);
                break;
            }
        }
        System.out.println(succeed ? "耶，成功了" : "不，失败了");
        int[] arr1 = generateRandomArray(maxSize, maxValue);
        int[] arr2 = copyArray(arr1);
        radixSort(arr1);
        comparator(arr2);
        printArray(arr1);
        printArray(arr2);
    }
}

```

# 二分法的详解与拓展

使用二分法不一定要有序，只要左右两侧求的跟问题有关而且确定能甩掉一半，就能使用二分法

## 在一个有序数组中，找某个数是否存在 

```java
public class Code04_BSExist {

	public static boolean exist(int[] sortedArr, int num) {
		if (sortedArr == null || sortedArr.length == 0) {
			return false;
		}
		int L = 0;
		int R = sortedArr.length - 1;
		int mid = 0;
		while (L < R) {
			mid = L + ((R - L) >> 1);
			if (sortedArr[mid] == num) {
				return true;
			} else if (sortedArr[mid] > num) {
				R = mid - 1;
			} else {
				L = mid + 1;
			}
		}
		return sortedArr[L] == num;
	}

}

```

## 在一个有序数组中，找>=某个数最左侧的位置

```java
public class Code05_BSNearLeft {

	// 在arr上，找满足>=value的最左位置
	public static int nearestIndex(int[] arr, int value) {
		int L = 0;
		int R = arr.length - 1;
		int index = -1;
		while (L < R) {
			int mid = L + ((R - L) >> 1);
			if (arr[mid] >= value) {
				index = mid;
				R = mid - 1;
			} else {
				L = mid + 1;
			}
		}
		return index;
	}

}

```

## 局部最小值问题（所有的数各不相等）

  ```java
  public class Code09_FindOneLessValueIndex {
  
  	public static int getLessIndex(int[] arr) {
          
          //一些特殊情况
  		if (arr == null || arr.length == 0) {
  			return -1; // no exist
  		}
  		if (arr.length == 1 || arr[0] < arr[1]) {
  			return 0;
  		}
  		if (arr[arr.length - 1] < arr[arr.length - 2]) {
  			return arr.length - 1;
  		}
  		int left = 1;
  		int right = arr.length - 2;
  		int mid = 0;
  		while (left < right) {
  			mid = (left + right) / 2;
  			if (arr[mid] > arr[mid - 1]) {  //中点左侧的值比中点的值小，所以只需考虑左边
  				right = mid - 1;
  			} else if (arr[mid] > arr[mid + 1]) { //中点的值小于左边大于右边
  				left = mid + 1;
  			} else {  //中点的值比左右两边都小
  				return mid;
  			}
  		}
          
          //如果left>=right,说明length<=3
          //又因为arr[0] < arr[1]和arr[arr.length - 1] < arr[arr.length - 2]都不成立，所以arr[1]最小
  		return left;
  	}
  
  	public static void printArray(int[] arr) {
  		for (int i = 0; i != arr.length; i++) {
  			System.out.print(arr[i] + " ");
  		}
  		System.out.println();
  	}
  
  	public static void main(String[] args) {
  		int[] arr = { 6, 5, 3, 4, 6, 7, 8 };
  		printArray(arr);
  		int index = getLessIndex(arr);
  		System.out.println("index: " + index + ", value: " + arr[index]);
  
  	}
  
  }
  
  ```

  

# 对数器的概念和使用 

1. 有一个你想要测的方法a 

2. 实现复杂度不好但是容易实现的方法b 

3. 实现一个随机样本产生器

4. 把方法a和方法b跑相同的随机样本，看看得到的结果是否一样

5. 如果有一个随机样本使得比对结果不一致，打印样本进行人工干预，改对方法a或者 方法b

6. 当样本数量很多时比对测试依然正确，可以确定方法a已经正确

```java
package class01;

import java.util.Arrays;

public class Code01_SelectionSort {

    //要测试的方法
	public static void selectionSort(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		for (int i = 0; i < arr.length - 1; i++) {
			int minIndex = i;
			for (int j = i + 1; j < arr.length; j++) {
				minIndex = arr[j] < arr[minIndex] ? j : minIndex;
			}
			swap(arr, i, minIndex);
		}
	}

    //交换两个数字
	public static void swap(int[] arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}

	// for test
    // 官方的排序方法
	public static void comparator(int[] arr) {
		Arrays.sort(arr);
	}

	// for test
    //生成随机的测试数组
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int testTime = 500000;  //测试次数
		int maxSize = 100;      //用于测试的数组的大小
		int maxValue = 100;     //生成的随机数的大小
		boolean succeed = true; //是否成功
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);  //生成随机测试数组
			int[] arr2 = copyArray(arr1);                          
			selectionSort(arr1);  //调用要测试的方法
			comparator(arr2);     //调用已有的方法
            //对结果进行比较
			if (!isEqual(arr1, arr2)) {
				succeed = false;
				printArray(arr1);
				printArray(arr2);
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");

		int[] arr = generateRandomArray(maxSize, maxValue);
		printArray(arr);
		selectionSort(arr);
		printArray(arr);
	}

}

```

# 比较器的使用 

1. 比较器的实质就是重载比较运算符
2. 比较器可以很好的应用在特殊标准的排序上
3. 比较器可以很好的应用在根据特殊标准排序的结构

```java
public static class Student{
	public String name;
	public int id;
	public int age;

	public Student(String name, int id, int age) {
		this.name = name;
		this.id = id;
		this.age = age;
	}

}

public static class IdAscendingComparator implements Comparator<Student> {
	//返回负数的时候，第一个参数排前面
	//返回正数的时候，第二个参数排前面
	//返回0的时候，谁排前面无所谓
	@Override
	public int compare(Student o1, Student o2) {
		return o1.id - o2.id;   //按id升序排列
	}

}

public static class IdDescendingComparator implements Comparator<Student> {

	@Override
	public int compare(Student o1, Student o2) {
		return o2.id - o1.id;   //按id降序排列
	}

}

public static class AgeAscendingComparator implements Comparator<Student> {

	@Override
	public int compare(Student o1, Student o2) {
		return o1.age - o2.age;
	}

}

public static class AgeDescendingComparator implements Comparator<Student> {

	@Override
	public int compare(Student o1, Student o2) {
		return o2.age - o1.age;
	}

}

public static void printStudents(Student[] students) {
	for (Student student : students) {
		System.out.println("Name : " + student.name + ", Id : " + student.id + ", Age : " + student.age);
	}
}

public static void printArray(Integer[] arr) {
	if (arr == null) {
		return;
	}
	for (int i = 0; i < arr.length; i++) {
		System.out.print(arr[i] + " ");
	}
	System.out.println();
}

public static class MyComp implements Comparator<Integer> {

	@Override
	public int compare(Integer o1, Integer o2) {
		return o2 - o1;
	}

}

public static void main(String[] args) {
	Student student1 = new Student("A", 2, 23);
	Student student2 = new Student("B", 3, 21);
	Student student3 = new Student("C", 1, 22);

	Student[] students = new Student[] { student1, student2, student3 };

	Arrays.sort(students, new IdAscendingComparator());
	printStudents(students);
	System.out.println("===========================");

	Arrays.sort(students, new IdDescendingComparator());
	printStudents(students);
	System.out.println("===========================");

	Arrays.sort(students, new AgeAscendingComparator());
	printStudents(students);
	System.out.println("===========================");

	Arrays.sort(students, new AgeDescendingComparator());
	printStudents(students);
	System.out.println("===========================");
	System.out.println("===========================");
	System.out.println("===========================");
	System.out.println("===========================");

	PriorityQueue<Student> maxHeapBasedAge = new PriorityQueue<>(new AgeDescendingComparator());
	maxHeapBasedAge.add(student1);
	maxHeapBasedAge.add(student2);
	maxHeapBasedAge.add(student3);
	while (!maxHeapBasedAge.isEmpty()) {
		Student student = maxHeapBasedAge.poll();
		System.out.println("Name : " + student.name + ", Id : " + student.id + ", Age : " + student.age);
	}
	System.out.println("===========================");

	PriorityQueue<Student> minHeapBasedId = new PriorityQueue<>(new IdAscendingComparator());
	minHeapBasedId.add(student1);
	minHeapBasedId.add(student2);
	minHeapBasedId.add(student3);
	while (!minHeapBasedId.isEmpty()) {
		Student student = minHeapBasedId.poll();
		System.out.println("Name : " + student.name + ", Id : " + student.id + ", Age : " + student.age);
	}
	System.out.println("===========================");
	System.out.println("===========================");
	System.out.println("===========================");

	TreeSet<Student> treeAgeDescending = new TreeSet<>(new AgeDescendingComparator());
	treeAgeDescending.add(student1);
	treeAgeDescending.add(student2);
	treeAgeDescending.add(student3);

	Student studentFirst = treeAgeDescending.first();
	System.out.println("Name : " + studentFirst.name + ", Id : " + studentFirst.id + ", Age : " + studentFirst.age);

	Student studentLast = treeAgeDescending.last();
	System.out.println("Name : " + studentLast.name + ", Id : " + studentLast.id + ", Age : " + studentLast.age);
	System.out.println("===========================");

}

```

# 递归行为时间复杂度估算

**master公式**：
$$
T(N) = a*T(N/b)+O(N^d)
$$

1. log(b,a) > d ---->复杂度为O(N ^log(b,a))
2. log(b,a) = d ---->复杂度为O(N^d*log(b,a))
3. log(b,a) < d ---->复杂度为O(N^d)
