<!-- GFM-TOC -->
* [快速选择](#快速选择)
* [堆](#堆)
    * [1. Kth Element](#1-kth-element)
* [桶排序](#桶排序)
    * [1. 出现频率最多的 k 个元素](#1-出现频率最多的-k-个元素)
    * [2. 按照字符出现次数对字符串排序](#2-按照字符出现次数对字符串排序)
* [荷兰国旗问题](#荷兰国旗问题)
    * [1. 按颜色进行排序](#1-按颜色进行排序)
<!-- GFM-TOC -->


# 快速选择

用于求解   **Kth Element**   问题，也就是第 K 个元素的问题。

可以使用快速排序的 partition() 进行实现。需要先打乱数组，否则最坏情况下时间复杂度为 O(N<sup>2</sup>)。

# 堆

用于求解   **TopK Elements**   问题，也就是 K 个最小元素的问题。可以维护一个大小为 K 的最小堆，最小堆中的元素就是最小元素。最小堆需要使用大顶堆来实现，大顶堆表示堆顶元素是堆中最大元素。这是因为我们要得到 k 个最小的元素，因此当遍历到一个新的元素时，需要知道这个新元素是否比堆中最大的元素更小，更小的话就把堆中最大元素去除，并将新元素添加到堆中。所以我们需要很容易得到最大元素并移除最大元素，大顶堆就能很好满足这个要求。

堆也可以用于求解 Kth Element 问题，得到了大小为 k 的最小堆之后，因为使用了大顶堆来实现，因此堆顶元素就是第 k 大的元素。

快速选择也可以求解 TopK Elements 问题，因为找到 Kth Element 之后，再遍历一次数组，所有小于等于 Kth Element 的元素都是 TopK Elements。

可以看到，快速选择和堆排序都可以求解 Kth Element 和 TopK Elements 问题。

## 1. Kth Element

215\. Kth Largest Element in an Array (Medium)

[Leetcode](https://leetcode.com/problems/kth-largest-element-in-an-array/description/) / [力扣](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/description/)

```text
Input: [3,2,1,5,6,4] and k = 2
Output: 5
```

题目描述：找到倒数第 k 个的元素。

#JAVA

**排序**  ：时间复杂度 O(NlogN)，空间复杂度 O(1)

```java
public int findKthLargest(int[] nums, int k) {
    Arrays.sort(nums);
    return nums[nums.length - k];
}
```

**堆**  ：时间复杂度 O(NlogK)，空间复杂度 O(K)。


```java
public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> pq = new PriorityQueue<>(); // 小顶堆
    for (int val : nums) {
        pq.add(val);
        if (pq.size() > k)  // 维护堆的大小为 K
            pq.poll();
    }
    return pq.peek();
}
```

**快速选择**  ：时间复杂度 O(N)，空间复杂度 O(1)

```java
public int findKthLargest(int[] nums, int k) {
    k = nums.length - k;
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int j = partition(nums, l, h);
        if (j == k) {
            break;
        } else if (j < k) {
            l = j + 1;
        } else {
            h = j - 1;
        }
    }
    return nums[k];
}

private int partition(int[] a, int l, int h) {
    int i = l, j = h + 1;
    while (true) {
        while (a[++i] < a[l] && i < h) ;
        while (a[--j] > a[l] && j > l) ;
        if (i >= j) {
            break;
        }
        swap(a, i, j);
    }
    swap(a, l, j);
    return j;
}

private void swap(int[] a, int i, int j) {
    int t = a[i];
    a[i] = a[j];
    a[j] = t;
}
```
#PHP
```php
方法一：堆排序（SPL标准库）
   /**
     * @param Integer[] $nums
     * @param Integer $k
     * @return Integer
     */
    function findKthLargest($nums, $k) {
        $h = new \SplMinHeap();
        foreach ($nums as $value) {
            $h->insert($value);
            if ($h->count() > $k) {
                $h->extract();
            }
        }
        return $h->top();
    }
方法二：冒泡排序
   /**
     * @param Integer[] $nums
     * @param Integer $k
     * @return Integer
     */
    function findKthLargest($nums, $k) {
         for($i=0; $i < $k; $i++){
            for($j = 0; $j < count($nums) - 1 - $i; $j++){
                if($nums[$j] > $nums[$j+1]){
                    $tmp = $nums[$j+1];
                    $nums[$j+1] = $nums[$j];
                    $nums[$j] = $tmp;
                }
            }
        }
        return $nums[$j];
    }
```
#GO
```go
func findKthLargest(nums []int, k int) int {
    nums = heapSort(nums)
    return nums[k-1]
}

func heapSort(nums []int) []int{
    lens := len(nums)
    //建堆,从非叶子结点开始调整
    for i := lens/2; i >= 0; i-- {
        down(nums, i, lens)
    }
    //将堆顶元素与末尾元素进行交换
    for i := lens-1; i >= 0; i-- {
        nums[0], nums[i] = nums[i], nums[0]
        lens--
        down(nums, 0, lens)
    }
    return nums
}

func down (nums []int, i, lens int){
    min := i  //i父节点
    left, right := 2*i+1, 2*i+2 //左，右孩子
    if left < lens && nums[left] < nums[min] {
        min = left
    }
    if right < lens && nums[right] < nums[min] {
        min = right
    }
    if min != i {
        nums[min], nums[i] = nums[i], nums[min]
        down(nums, min, lens)
    }
}
```

# 桶排序

## 1. 出现频率最多的 k 个元素

347\. Top K Frequent Elements (Medium)

[Leetcode](https://leetcode.com/problems/top-k-frequent-elements/description/) / [力扣](https://leetcode-cn.com/problems/top-k-frequent-elements/description/)

```html
Given [1,1,1,2,2,3] and k = 2, return [1,2].
```

设置若干个桶，每个桶存储出现频率相同的数。桶的下标表示数出现的频率，即第 i 个桶中存储的数出现的频率为 i。

把数都放到桶之后，从后向前遍历桶，最先得到的 k 个数就是出现频率最多的的 k 个数。

#JAVA
```java
public List<Integer> topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> frequencyForNum = new HashMap<>();
    for (int num : nums) {
        frequencyForNum.put(num, frequencyForNum.getOrDefault(num, 0) + 1);
    }
    List<Integer>[] buckets = new ArrayList[nums.length + 1];
    for (int key : frequencyForNum.keySet()) {
        int frequency = frequencyForNum.get(key);
        if (buckets[frequency] == null) {
            buckets[frequency] = new ArrayList<>();
        }
        buckets[frequency].add(key);
    }
    List<Integer> topK = new ArrayList<>();
    for (int i = buckets.length - 1; i >= 0 && topK.size() < k; i--) {
        if (buckets[i] == null) {
            continue;
        }
        if (buckets[i].size() <= (k - topK.size())) {
            topK.addAll(buckets[i]);
        } else {
            topK.addAll(buckets[i].subList(0, k - topK.size()));
        }
    }
    return topK;
}
```
#PHP
```php
   /**
     * @param Integer[] $nums
     * @param Integer $k
     * @return Integer[]
     */
    function topKFrequent($nums, $k) {
        $nums = array_count_values($nums);//统计数组中所有的值
        $pq = new SplPriorityQueue;
        foreach($nums as $key=>$val){
            $pq->insert($key, $val);
        } 
        $nums = [];
        for($i=0; $i<$k; $i++) {
            $nums[] = $pq->extract();
        }    
        return $nums;
    }
```
#GO
```go
type Feq struct {
	val int
	cnt int
}
type FeqMinHeap []Feq

func (pq *FeqMinHeap) Len() int {
	return len(*pq)
}
func (pq *FeqMinHeap) Less(i, j int) bool {
	return (*pq)[i].cnt < (*pq)[j].cnt
}
func (pq *FeqMinHeap) Swap(i, j int) {
	(*pq)[i], (*pq)[j] = (*pq)[j], (*pq)[i]
}

func (pq *FeqMinHeap) Push(x interface{}) {
	*pq = append(*pq, x.(Feq))
}

func (pq *FeqMinHeap) Pop() interface{} {
	n := len(*pq) - 1
	x := (*pq)[n]
	*pq = (*pq)[:n]
	return x
}
func (pq *FeqMinHeap) Peek() Feq {
	return (*pq)[0]
}

func topKFrequent(nums []int, k int) []int {
	cntMap := make(map[int]int)
	for _, num := range nums {
		cntMap[num]++
	}
	minHeap := &FeqMinHeap{}
	for key, val := range cntMap {
		heap.Push(minHeap, Feq{
			val: key,
			cnt: val,
		})
		if minHeap.Len() > k {
			heap.Pop(minHeap)
		}
	}
	res := make([]int, k)
	i := 0
	for minHeap.Len() > 0 {
		res[i] = minHeap.Pop().(Feq).val
		i++
	}
	return res
}
```
## 2. 按照字符出现次数对字符串排序

451\. Sort Characters By Frequency (Medium)

[Leetcode](https://leetcode.com/problems/sort-characters-by-frequency/description/) / [力扣](https://leetcode-cn.com/problems/sort-characters-by-frequency/description/)

```html
Input:
"tree"

Output:
"eert"

Explanation:
'e' appears twice while 'r' and 't' both appear once.
So 'e' must appear before both 'r' and 't'. Therefore "eetr" is also a valid answer.
```
#JAVA
```java
public String frequencySort(String s) {
    Map<Character, Integer> frequencyForNum = new HashMap<>();
    for (char c : s.toCharArray())
        frequencyForNum.put(c, frequencyForNum.getOrDefault(c, 0) + 1);

    List<Character>[] frequencyBucket = new ArrayList[s.length() + 1];
    for (char c : frequencyForNum.keySet()) {
        int f = frequencyForNum.get(c);
        if (frequencyBucket[f] == null) {
            frequencyBucket[f] = new ArrayList<>();
        }
        frequencyBucket[f].add(c);
    }
    StringBuilder str = new StringBuilder();
    for (int i = frequencyBucket.length - 1; i >= 0; i--) {
        if (frequencyBucket[i] == null) {
            continue;
        }
        for (char c : frequencyBucket[i]) {
            for (int j = 0; j < i; j++) {
                str.append(c);
            }
        }
    }
    return str.toString();
}
```
#PHP
```php
   /**
     * @param String $s
     * @return String
     */
    function frequencySort($s) {
        $row = array_count_values(str_split($s));
        arsort($row);
        $s = '';
        foreach($row as $str=>$nums){
            $s .= str_repeat($str, $nums);
        }
        return $s;
    }
```
#GO
```go
import (
    "strings"
    "sort"
)
func frequencySort(s string) string {
    strs := strings.Split(s, "")
    strsMap := make(map[string]string)
    for i := 0; i < len(strs); i++ {
        strsMap[strs[i]] += strs[i]//字符串拼接
    }
    //把strsMap转化成可排序的strList
    strList := make([]string, 0, len(strsMap))
    for _, val := range strsMap {
        strList = append(strList, val)
    }
    // 对strList的value进行排序
    sort.Slice(strList, func(i, j int) bool {
        return len(strList[i]) >= len(strList[j])
    })
    // 返回拼接的字符串
    return strings.Join(strList, "")
}
```

# 荷兰国旗问题

荷兰国旗包含三种颜色：红、白、蓝。

有三种颜色的球，算法的目标是将这三种球按颜色顺序正确地排列。它其实是三向切分快速排序的一种变种，在三向切分快速排序中，每次切分都将数组分成三个区间：小于切分元素、等于切分元素、大于切分元素，而该算法是将数组分成三个区间：等于红色、等于白色、等于蓝色。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/7a3215ec-6fb7-4935-8b0d-cb408208f7cb.png"/> </div><br>


## 1. 按颜色进行排序

75\. Sort Colors (Medium)

[Leetcode](https://leetcode.com/problems/sort-colors/description/) / [力扣](https://leetcode-cn.com/problems/sort-colors/description/)

```html
Input: [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

题目描述：只有 0/1/2 三种颜色。

#JAVA
```java
public void sortColors(int[] nums) {
    int zero = -1, one = 0, two = nums.length;
    while (one < two) {
        if (nums[one] == 0) {
            swap(nums, ++zero, one++);
        } else if (nums[one] == 2) {
            swap(nums, --two, one);
        } else {
            ++one;
        }
    }
}

private void swap(int[] nums, int i, int j) {
    int t = nums[i];
    nums[i] = nums[j];
    nums[j] = t;
}
```
#PHP
```php
   /**
     * @param Integer[] $nums
     * @return NULL
     */
    function sortColors(&$nums) {
        $left = 0;
        $len = count($nums) -1;
        for ($i=0; $i<= $len;) {
            if ($nums[$i] == 0) {
                [$nums[$i], $nums[$left]] = [$nums[$left], $nums[$i]];
                $left++;
                $i++;
            }elseif($nums[$i] == 1){
                $i++;
            }else{          
                [$nums[$i], $nums[$len]] = [$nums[$len], $nums[$i]];
                $len--;
            }
        }
    }
```
#GO
```go
func sortColors(nums []int) {
    left, len := 0, len(nums)-1
    for i := 0; i <= len; {
        if nums[i] == 0{
            nums[i], nums[left] = nums[left], nums[i]
            left++
            i++
        }else if nums[i] == 1{
            i++
        }else{
            nums[i], nums[len] = nums[len], nums[i]
            len--
        }
    }
}
```




<div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/githubio/公众号二维码-2.png"></img></div>
