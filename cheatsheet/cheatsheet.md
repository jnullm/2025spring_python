# 笔记
## 一、bfs
### 1.deque实现
```python
from collections import deque
q = deque([12,43,54])
#append队列尾端添加元素
q.append()
#popleft弹出最左边元素
q.popleft()
#pop弹出最右边元素
q.pop()
```
### 2.dijkstra算法heapq实现，最小权值路径
不同于bfs模板访问后就不再通过，dijkstra创建了一个权值表格，当再次经过此时，只有更小的权值才能覆盖这个点，否则就忽略这次访问，同时代替了visited的作用
```python
import heapq
pq = []
#heappush往pq里添加
heapq.heappush(pq, (5,6))
#heappop弹出字典序最小的元素，元组可以直接用‘=’来承接
a, b = heapq.heappop(pq)  
lis = [1, 2, 3]
heapq.heqpify(lis) #形成heapq
pq[0] #直接查看heapq最小元素
data = [5, 7, 9, 1, 3]
print(heapq.nsmallest(3, data))  # 输出: [1, 3, 5]
print(heapq.nlargest(3, data))   # 输出: [9, 7, 5]
```
### 3.集合实现visited加快访问速度
```python
visited = set()
#经历过某个点
visited.add(point)
```
## 二、dfs
与bfs没有本质区别，一条路走到死看是否需要回溯，回溯可以直接return，回溯的时候记得把原本的下一步删掉，或者干脆就不走了，比如在地图上走，走完这一条路回来的时候记得把地图上这个点重新标为可以走
## 三、dp
### 1. 01背包，取或不取
将取这个和不取这个的总价值进行比较，取最高的，dp从而保证一定最优
```python
n, b = map(int, input().split())
value = list(map(int, input().split()))
weight = list(map(int, input().split()))
dp = [[0 for _ in range(b+1)] for _ in range(n+1)]
for i in range(1, n+1):
    for j in range(b+1):
        if weight[i-1] <= j:
            dp[i][j] = max(dp[i-1][j-weight[i-1]]+value[i-1], dp[i-1][j])
print(dp[-1][-1])
```
### 2. 无限背包
遍历dp每一个值，每一个值的时候遍历每一个物件，反正重复了没事
```python
 n, a, b, c = map(int, input().split())
 dp = [float('-inf')]*n
 for i in range(1, n+1):
    for j in (a, b, c):
        if i >= j:
            dp[i] = max(dp[i-j] + 1, dp[i])
 print(dp[n])
```
### 3. 收服小精灵
```python
n, m, k = map(int, input().split())
dp = [[-1 for _ in range(m+1)] for _ in range(k+1)]
dp[0][m] = n
max_life = 0
max_n = 0
for x in range(1, k+1):
    cnt, life = map(int, input().split())
    for i in range(m+1):
        for j in range(x, 0, -1):
            if i + life <= m and dp[j-1][i+life] != -1:
                dp[j][i] = max(dp[j][i], dp[j-1][i+life]-cnt)
                if j > max_n:
                    max_n = j
                    max_life = i
                elif j == max_n:
                    if max_life < i:
                        max_life = i
print(max_n, max_life)
```

## 四、字典树

```python
class TrieNode:
    def __init__(self):
        self.child = {}
        self.is_last = False


class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, number):
        node = self.root
        for s in number:
            if s not in node.child:
                node.child[s] = TrieNode()
            node = node.child[s]
            if node.is_last:
                return False
        node.is_last = True
        return True

    def judge(self, numbers):
        for num in numbers:
            if not self.insert(num):
                return False
        return True


t = int(input())
ans = []
for _ in range(t):
    m = int(input())
    orig = [input() for _ in range(m)]

    trie = Trie()
    orig.sort()
    if trie.judge(orig):
        ans.append("YES")
    else:
        ans.append("NO")
for s in ans:
    print(s)
```

## 五、拓扑排序

```python
t = int(input())
ans = []
for _ in range(t):
    n, m = map(int, input().split())
    dic1 = {i: [] for i in range(1, n+1)}
    dic2 = {i: 0 for i in range(1, n + 1)}
    for _ in range(m):
        a, b = map(int, input().split())
        dic1[b].append(a) #加入被指的
        dic2[a] += 1 #指别人的次数

    cnt = 0
    visited = set()
    arb = 0
    while cnt < n:
        arb = 0
        for i in range(1, n+1):
            if dic2[i] == 0 and i not in visited: #使没指别人的优先退出
                for j in dic1[i]:
                    dic2[j] -= 1
                arb = 1
                visited.add(i)
        if arb == 0:
            break
```

## 六、MST/PRIM

```python
import heapq

while True:
    try:
        n = int(input())
    except EOFError:
        break

    dist = [list(map(int, input().split())) for _ in range(n)]
    d = [100000 for _ in range(n)]
    d[0] = 0
    visited = set()
    q = [(d[0], 0)]
    cnt = 0

    while q:
        l, ori = heapq.heappop(q)
        if ori in visited:
            continue

        visited.add(ori)
        cnt += l
        for i in range(n):
            if d[i] > dist[ori][i]:
                d[i] = dist[ori][i]
                heapq.heappush(q, (d[i], i))

    print(cnt)
```

## 七、二叉树

前序遍历：根-左-右，中序遍历：左-根-右， 后序遍历：左-右-根

## 八、并查集

```python
father = []
def get_father(x):
    if father[x] != x:
        return get_father(father[x]) #通过递归来创造一个父节点
    return x


cnt = 0
while True:
    cnt += 1
    n, m = map(int, input().split())
    if n == 0:
        break

    father = [i for i in range(n)]
    for _ in range(m):
        a, b = map(int, input().split())
        a -= 1
        b -= 1
        if get_father(a) != get_father(b):
            father[get_father(a)] = get_father(b) #连接各自的父节点
    pp = 0
    for i in range(n):
        if father[i] == i:
            pp += 1
    print("Case "+str(cnt)+": "+str(pp))
```

## 九、归并排序

```python
cnt = 0
def merge_sort(s):
    global cnt

    n = len(s)
    if n == 1:
        return s
    if n == 2:
        if s[0] > s[1]:
            cnt += 1
            s = s[::-1]
        return s

    mid = n // 2
    a = merge_sort(s[:mid])
    b = merge_sort(s[mid:])

    ans = []
    pnt1 = 0
    pnt2 = 0
    while pnt1 < mid and pnt2 < n-mid:
        if a[pnt1] < b[pnt2]:
            ans.append(a[pnt1])
            pnt1 += 1
        else:
            ans.append(b[pnt2])
            cnt += mid - pnt1
            pnt2 += 1
    ans = ans + a[pnt1:] + b[pnt2:]
    return ans
```

## 十、中序转后序表达式

```python
sym = ["+", "-", "*", "/", "(", ")"]
rank = {"+": 0, "-": 0, "*": 1, "/": 1}
def change(s):
    i = 0
    n = len(s)
    opera = []
    ans = []
    while i < n:
        if s[i] in sym[:-2]:
            while opera and opera[-1] != "(" and rank[opera[-1]] >= rank[s[i]]:
                ans.append(opera.pop())
            opera.append(s[i])
        elif s[i] == "(":
            opera.append("(")
        elif s[i] == ")":
            while opera and opera[-1] != "(":
                ans.append(opera.pop())
            opera.pop()
        else:
            j = i
            while i < n-1 and s[i+1] not in sym:
                i += 1
            ans.append(s[j:i+1])
        i += 1
    while opera:
        if opera[-1] != "(":
            ans.append(opera.pop())
        else:
            opera.pop()
    return " ".join(ans)


N = int(input())
for _ in range(N):
    print(change(input()))
```

## 十一、层序遍历建树

```python
n = int(input())
num = list(map(int, input().split()))
dic = {num[0]: TreeNode(num[0])}
for i in range(n):
    x1 = 2*i + 1
    x2 = 2*i + 2
    if x1 < n:
        dic[num[x1]] = TreeNode(num[x1])
        dic[num[i]].left = dic[num[x1]]
    if x2 < n:
        dic[num[x2]] = TreeNode(num[x2])
        dic[num[i]].right = dic[num[x2]]
```

## 十二、Bellman Ford

```python
class Solution:
    def findCheapestPrice(self, n: int, flights: List[List[int]], src: int, dst: int, k: int) -> int:
        dist = [float("inf")] * n
        dist[src] = 0
        for _ in range(k + 1):
            prev = dist[:]  
            for u, v, w in flights:
                if prev[u] + w < dist[v]:
                    dist[v] = prev[u] + w
        
        return dist[dst] if dist[dst] != float("inf") else -1
```

## 十三、Haffman

```python
n = int(input())
elem = []
heapq.heapify(elem)
for i in range(n):
    st, hev = input().split()
    heapq.heappush(elem, (int(hev), st, TreeNode(st)))  #权重，字符集里最小字符，字符本身
while len(elem) > 1:
    hev1, st1, node1 = heapq.heappop(elem)
    hev2, st2, node2 = heapq.heappop(elem)
    node0 = TreeNode(node1.val+node2.val)
    node0.left = node1
    node0.right = node2
    heapq.heappush(elem, (hev1+hev2, min(st1, st2), node0))
x1, x2, root = heapq.heappop(elem)
```

## 十四、Kadane-最大子矩阵

```python
ans = float("-inf")
for i in range(n):
    num = [0 for _ in range(n)]
    for j in range(i, n):
        for k in range(n):
            num[k] += mat[k][j]
        his_max = num[0]
        cur_max = num[0]
        for q in range(1, n):
            cur_max = max(num[q], num[q]+cur_max)
            his_max = max(cur_max, his_max)
        ans = max(his_max, ans)
print(ans)
```

## 十五、KMP

```python
def kmp_next(s):
  	# kmp算法计算最长相等前后缀
    next = [0] * len(s)
    j = 0
    for i in range(1, len(s)):
        while s[i] != s[j] and j > 0:
            j = next[j - 1]
        if s[i] == s[j]:
            j += 1
        next[i] = j
    return next


def main():
    case = 0
    while True:
        n = int(input().strip())
        if n == 0:
            break
        s = input().strip()
        case += 1
        print("Test case #{}".format(case))
        next = kmp_next(s)
        for i in range(2, len(s) + 1):
            k = i - next[i - 1]		# 可能的重复子串的长度
            if (i % k == 0) and i // k > 1:
                print(i, i // k)
        print()
```

## 二、技术问题

### 1.format()
```python
#把一个数变为位数更高的浮点数
"{:.10f}".format(number)
```
### 2.bisect
```python
import bisect
#lis是有序的
lis = input().split()
#bisect_left若x在里面，给出lis中最左边x的索引值
a = bisect.bisect_left(lis, x)
#bisect_right若x在里面，给出lis中最右边x的索引值
b = bisect.bisect_right(lis, x)
#二者都保证插入后列表仍有序，先插好再给里面各自x的索引值
```
### 3.冒泡排序
本质是从最后一位排起，遍历一遍一定能把最大的找到并移到最后
```python
lis = list(map(int, input().split()))
for i in range(len(lis)):
    for j in range(len(lis)-i-1):
        if lis[j] > lis[j+1]:
            lis[j], lis[j+1] = lis[j+1], lis[j]
print(lis)
```
### 4.deepcopy
```python
#用深拷贝安全修改新列表而不影响旧列表
from copy import deepcopy
n, m = map(int, input().split())
old = [[0 for _ in range(m+2)]] + [[0]+list(map(int, input().split()))+[0] for _ in range(n)] + [[0 for _ in range(m+2)]]
new = deepcopy(old)
```
### 6.集合
```python
s = set(lis) #若列表为空则生成空集
s = {1,2,3} #手动生成
s.add(element)
s.remove(element) #没有会报错
s2 = s1.union(s)
s2 = s1.intersection(s)
```
### 7.防止忘记
```python
#pop，删除指定索引处，默认-1
lis.pop(index)
a = lis.index(element)#第一个，没有会报错
n = lis.count(element)
#字典默认循环的是key
a = ord(b) #获取ascII码
b = chr(a)
lis = input(" ", 1)#后面一个数表示分割的次数
```
### 8.一次性读取
```python
#Ctrl+D结束输入，在本地可操作
import sys
input_data = sys.stdin.read().split()
#得到的是一个一维表格，所有的" "与换行符都被去掉了，要用一个数来确定读到哪个数据了
```
### 9. 埃氏筛&欧拉筛
```python
#埃氏筛核心思想是从小的素数开始，将其倍数标为非素数，剩下的就是素数了
arbit = [False,False,True,True] + [False,True]*500000
i = 3
while i**2 <= 1000000:
    if arbit[i-1] == True:
        for j in range(i*2, 1000001, i):
            arbit[j-1] = False
    i += 1
#arbit就是素数列表

#欧拉筛基本是埃氏筛进化思想，排除一个数被反复筛，也就是每次筛停住，只构造质数相乘情况
def oula(r):
    # 全部初始化为0
    prime = [0 for i in range(r+1)]
    # 存放素数
    common = []
    for i in range(2, r+1):
        if prime[i] == 0:
            common.append(i)
        for j in common:
            if i*j > r:
                break
            prime[i*j] = 1
            #将重复筛选剔除
            if i % j == 0:
                break
    return common
#commen就是素数列表,对于扫10^6内的埃氏筛只用扫1000,欧拉筛要扫1000000,故而埃氏筛更快
```
