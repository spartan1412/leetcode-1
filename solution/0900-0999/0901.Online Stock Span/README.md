# [901. 股票价格跨度](https://leetcode.cn/problems/online-stock-span)

[English Version](/solution/0900-0999/0901.Online%20Stock%20Span/README_EN.md)

## 题目描述

<!-- 这里写题目描述 -->

<p>编写一个 <code>StockSpanner</code> 类，它收集某些股票的每日报价，并返回该股票当日价格的跨度。</p>

<p>今天股票价格的跨度被定义为股票价格小于或等于今天价格的最大连续日数（从今天开始往回数，包括今天）。</p>

<p>例如，如果未来7天股票的价格是 <code>[100, 80, 60, 70, 60, 75, 85]</code>，那么股票跨度将是 <code>[1, 1, 1, 2, 1, 4, 6]</code>。</p>

<p>&nbsp;</p>

<p><strong>示例：</strong></p>

<pre><strong>输入：</strong>[&quot;StockSpanner&quot;,&quot;next&quot;,&quot;next&quot;,&quot;next&quot;,&quot;next&quot;,&quot;next&quot;,&quot;next&quot;,&quot;next&quot;], [[],[100],[80],[60],[70],[60],[75],[85]]
<strong>输出：</strong>[null,1,1,1,2,1,4,6]
<strong>解释：</strong>
首先，初始化 S = StockSpanner()，然后：
S.next(100) 被调用并返回 1，
S.next(80) 被调用并返回 1，
S.next(60) 被调用并返回 1，
S.next(70) 被调用并返回 2，
S.next(60) 被调用并返回 1，
S.next(75) 被调用并返回 4，
S.next(85) 被调用并返回 6。

注意 (例如) S.next(75) 返回 4，因为截至今天的最后 4 个价格
(包括今天的价格 75) 小于或等于今天的价格。
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ol>
	<li>调用&nbsp;<code>StockSpanner.next(int price)</code>&nbsp;时，将有&nbsp;<code>1 &lt;= price &lt;= 10^5</code>。</li>
	<li>每个测试用例最多可以调用&nbsp; <code>10000</code> 次 <code>StockSpanner.next</code>。</li>
	<li>在所有测试用例中，最多调用&nbsp;<code>150000</code>&nbsp;次&nbsp;<code>StockSpanner.next</code>。</li>
	<li>此问题的总时间限制减少了 50%。</li>
</ol>

## 解法

<!-- 这里可写通用的实现逻辑 -->

**方法一：单调栈**

根据题目描述，我们可以知道，对于当日价格 `price`，从这个价格开始往前找，找到第一个比这个价格大的价格，这两个价格的下标差 `cnt` 就是当日价格的跨度。

这实际上是经典的单调栈模型，找出左侧第一个比当前元素大的元素。

我们维护一个从栈底到栈顶价格单调递减的栈，栈中每个元素存放的是 `(price, cnt)` 数据对，其中 `price` 表示价格，`cnt` 表示当前价格的跨度。

出现价格 `price` 时，我们将其与栈顶元素进行比较，如果栈顶元素的价格小于等于 `price`，则将当日价格的跨度 `cnt` 加上栈顶元素的跨度，然后将栈顶元素出栈，直到栈顶元素的价格大于 `price`，或者栈为空为止。

最后将 `(price, cnt)` 入栈，返回 `cnt` 即可。

时间复杂度 $O(n)$，其中 $n$ 为 `next` 函数的调用次数。

<!-- tabs:start -->

### **Python3**

<!-- 这里可写当前语言的特殊实现逻辑 -->

```python
class StockSpanner:
    def __init__(self):
        self.stk = []

    def next(self, price: int) -> int:
        cnt = 1
        while self.stk and self.stk[-1][0] <= price:
            cnt += self.stk.pop()[1]
        self.stk.append((price, cnt))
        return cnt


# Your StockSpanner object will be instantiated and called as such:
# obj = StockSpanner()
# param_1 = obj.next(price)
```

### **Java**

<!-- 这里可写当前语言的特殊实现逻辑 -->

```java
class StockSpanner {
    private Deque<int[]> stk = new ArrayDeque<>();

    public StockSpanner() {

    }

    public int next(int price) {
        int cnt = 1;
        while (!stk.isEmpty() && stk.peek()[0] <= price) {
            cnt += stk.pop()[1];
        }
        stk.push(new int[] {price, cnt});
        return cnt;
    }
}

/**
 * Your StockSpanner object will be instantiated and called as such:
 * StockSpanner obj = new StockSpanner();
 * int param_1 = obj.next(price);
 */
```

### **C++**

```cpp
class StockSpanner {
public:
    StockSpanner() {

    }

    int next(int price) {
        int cnt = 1;
        while (!stk.empty() && stk.top().first <= price) {
            cnt += stk.top().second;
            stk.pop();
        }
        stk.push({price, cnt});
        return cnt;
    }

private:
    stack<pair<int, int>> stk;
};

/**
 * Your StockSpanner object will be instantiated and called as such:
 * StockSpanner* obj = new StockSpanner();
 * int param_1 = obj->next(price);
 */
```

### **Go**

```go
type StockSpanner struct {
	stk []pair
}

func Constructor() StockSpanner {
	return StockSpanner{[]pair{}}
}

func (this *StockSpanner) Next(price int) int {
	cnt := 1
	for len(this.stk) > 0 && this.stk[len(this.stk)-1].price <= price {
		cnt += this.stk[len(this.stk)-1].cnt
		this.stk = this.stk[:len(this.stk)-1]
	}
	this.stk = append(this.stk, pair{price, cnt})
	return cnt
}

type pair struct{ price, cnt int }

/**
 * Your StockSpanner object will be instantiated and called as such:
 * obj := Constructor();
 * param_1 := obj.Next(price);
 */
```

### **TypeScript**

```ts
class StockSpanner {
    private stack: [number, number][];

    constructor() {
        this.stack = [[Infinity, -1]];
    }

    next(price: number): number {
        let res = 1;
        while (this.stack[this.stack.length - 1][0] <= price) {
            res += this.stack.pop()[1];
        }
        this.stack.push([price, res]);
        return res;
    }
}

/**
 * Your StockSpanner object will be instantiated and called as such:
 * var obj = new StockSpanner()
 * var param_1 = obj.next(price)
 */
```

### **Rust**

```rust
use std::collections::VecDeque;
struct StockSpanner {
    stack: VecDeque<(i32, i32)>,
}


/**
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl StockSpanner {
    fn new() -> Self {
        Self {
            stack: vec![(i32::MAX, -1)].into_iter().collect()
        }
    }

    fn next(&mut self, price: i32) -> i32 {
        let mut res = 1;
        while self.stack.back().unwrap().0 <= price {
            res += self.stack.pop_back().unwrap().1;
        }
        self.stack.push_back((price, res));
        res
    }
}

/**
 * Your StockSpanner object will be instantiated and called as such:
 * let obj = StockSpanner::new();
 * let ret_1: i32 = obj.next(price);
 */
```

### **...**

```

```

<!-- tabs:end -->
