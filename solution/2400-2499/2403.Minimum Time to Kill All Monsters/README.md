# [2403. Minimum Time to Kill All Monsters](https://leetcode.cn/problems/minimum-time-to-kill-all-monsters)

[English Version](/solution/2400-2499/2403.Minimum%20Time%20to%20Kill%20All%20Monsters/README_EN.md)

## 题目描述

<!-- 这里写题目描述 -->

<p>You are given an integer array <code>power</code> where <code>power[i]</code> is the power of the <code>i<sup>th</sup></code> monster.</p>

<p>You start with <code>0</code> mana points, and each day you increase your mana points by <code>gain</code> where <code>gain</code> initially is equal to <code>1</code>.</p>

<p>Each day, after gaining <code>gain</code> mana, you can defeat a monster if your mana points are greater than or equal to the power of that monster. When you defeat a monster:</p>

<ul>
	<li>your mana points will be reset to <code>0</code>, and</li>
	<li>the value of <code>gain</code> increases by <code>1</code>.</li>
</ul>

<p>Return <em>the <strong>minimum</strong> number of days needed to defeat all the monsters.</em></p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input:</strong> power = [3,1,4]
<strong>Output:</strong> 4
<strong>Explanation:</strong> The optimal way to beat all the monsters is to:
- Day 1: Gain 1 mana point to get a total of 1 mana point. Spend all mana points to kill the 2<sup>nd</sup> monster.
- Day 2: Gain 2 mana points to get a total of 2 mana points.
- Day 3: Gain 2 mana points to get a total of 4 mana points. Spend all mana points to kill the 3<sup>rd</sup> monster.
- Day 4: Gain 3 mana points to get a total of 3 mana points. Spend all mana points to kill the 1<sup>st</sup> monster.
It can be proven that 4 is the minimum number of days needed. 
</pre>

<p><strong class="example">Example 2:</strong></p>

<pre>
<strong>Input:</strong> power = [1,1,4]
<strong>Output:</strong> 4
<strong>Explanation:</strong> The optimal way to beat all the monsters is to:
- Day 1: Gain 1 mana point to get a total of 1 mana point. Spend all mana points to kill the 1<sup>st</sup> monster.
- Day 2: Gain 2 mana points to get a total of 2 mana points. Spend all mana points to kill the 2<sup>nd</sup> monster.
- Day 3: Gain 3 mana points to get a total of 3 mana points.
- Day 4: Gain 3 mana points to get a total of 6 mana points. Spend all mana points to kill the 3<sup>rd</sup> monster.
It can be proven that 4 is the minimum number of days needed. 
</pre>

<p><strong class="example">Example 3:</strong></p>

<pre>
<strong>Input:</strong> power = [1,2,4,9]
<strong>Output:</strong> 6
<strong>Explanation:</strong> The optimal way to beat all the monsters is to:
- Day 1: Gain 1 mana point to get a total of 1 mana point. Spend all mana points to kill the 1st monster.
- Day 2: Gain 2 mana points to get a total of 2 mana points. Spend all mana points to kill the 2nd monster.
- Day 3: Gain 3 mana points to get a total of 3 mana points.
- Day 4: Gain 3 mana points to get a total of 6 mana points.
- Day 5: Gain 3 mana points to get a total of 9 mana points. Spend all mana points to kill the 4th monster.
- Day 6: Gain 4 mana points to get a total of 4 mana points. Spend all mana points to kill the 3rd monster.
It can be proven that 6 is the minimum number of days needed.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= power.length &lt;= 17</code></li>
	<li><code>1 &lt;= power[i] &lt;= 10<sup>9</sup></code></li>
</ul>

## 解法

<!-- 这里可写通用的实现逻辑 -->

**方法一：状态压缩 + 动态规划**

由于打怪才能增加每天法力的收益 `gain`，不同的打怪顺序对结果有影响，需要枚举。注意到题目的数据范围较小，考虑使用状态压缩动态规划求解。

我们定义状态 `mask` 表示当前已经打怪的情况，其二进制中的 `1` 表示已经被打倒的怪物，`0` 表示未被打倒的怪物。

时间复杂度 $O(n\times 2^n)$，空间复杂度 $O(2^n)$。其中 $n$ 是怪物数量。

<!-- tabs:start -->

### **Python3**

<!-- 这里可写当前语言的特殊实现逻辑 -->

```python
class Solution:
    def minimumTime(self, power: List[int]) -> int:
        @cache
        def dfs(mask):
            cnt = mask.bit_count()
            if cnt == len(power):
                return 0
            ans = inf
            for i, v in enumerate(power):
                if mask & (1 << i):
                    continue
                ans = min(ans, dfs(mask | 1 << i) + (v + cnt) // (cnt + 1))
            return ans

        return dfs(0)
```

### **Java**

<!-- 这里可写当前语言的特殊实现逻辑 -->

```java
class Solution {
    private int n;
    private long[] f;
    private int[] power;

    public long minimumTime(int[] power) {
        n = power.length;
        f = new long[1 << n];
        Arrays.fill(f, -1);
        this.power = power;
        return dfs(0);
    }

    private long dfs(int mask) {
        if (f[mask] != -1) {
            return f[mask];
        }
        int cnt = Integer.bitCount(mask);
        if (cnt == n) {
            return 0;
        }
        long ans = Long.MAX_VALUE;
        for (int i = 0; i < n; ++i) {
            if (((mask >> i) & 1) == 1) {
                continue;
            }
            ans = Math.min(ans, dfs(mask | 1 << i) + (power[i] + cnt) / (cnt + 1));
        }
        f[mask] = ans;
        return ans;
    }
}
```

### **C++**

```cpp
using ll = long long;

class Solution {
public:
    vector<ll> f;
    vector<int> power;
    int n;

    long long minimumTime(vector<int>& power) {
        n = power.size();
        f.assign(1 << n, -1);
        this->power = power;
        return dfs(0);
    }

    ll dfs(int mask) {
        if (f[mask] != -1) return f[mask];
        int cnt = __builtin_popcount(mask);
        if (cnt == n) return 0;
        ll ans = LONG_MAX;
        for (int i = 0; i < n; ++i) {
            if ((mask >> i) & 1) continue;
            ans = min(ans, dfs(mask | 1 << i) + (power[i] + cnt) / (cnt + 1));
        }
        f[mask] = ans;
        return ans;
    }
};
```

### **Go**

```go
func minimumTime(power []int) int64 {
	n := len(power)
	f := make([]int64, 1<<n)
	for i := range f {
		f[i] = -1
	}
	var dfs func(mask int) int64
	dfs = func(mask int) int64 {
		if f[mask] != -1 {
			return f[mask]
		}
		cnt := bits.OnesCount(uint(mask))
		if cnt == n {
			return 0
		}
		var ans int64 = math.MaxInt64
		for i, v := range power {
			if (mask >> i & 1) == 1 {
				continue
			}
			ans = min(ans, dfs(mask|1<<i)+int64((v+cnt)/(cnt+1)))
		}
		f[mask] = ans
		return ans
	}
	return dfs(0)
}

func min(a, b int64) int64 {
	if a < b {
		return a
	}
	return b
}
```

### **TypeScript**

```ts
function minimumTime(power: number[]): number {
    const n = power.length;
    const f = new Array(1 << n).fill(-1);
    function dfs(mask) {
        if (f[mask] != -1) {
            return f[mask];
        }
        const cnt = bitCount(mask);
        if (cnt == n) {
            return 0;
        }
        let ans = Infinity;
        for (let i = 0; i < n; ++i) {
            if ((mask >> i) & 1) {
                continue;
            }
            ans = Math.min(
                ans,
                dfs(mask | (1 << i)) + Math.ceil(power[i] / (cnt + 1)),
            );
        }
        f[mask] = ans;
        return ans;
    }
    return dfs(0);
}

function bitCount(x) {
    let cnt = 0;
    for (let i = 0; i < 32; ++i) {
        if ((x >> i) & 1) {
            ++cnt;
        }
    }
    return cnt;
}
```

### **...**

```


```

<!-- tabs:end -->