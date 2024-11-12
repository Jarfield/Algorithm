# lab3

## Q1 女巫的草药田

### 解题思路

1. **问题定义**：
   - 给定一个大小为 n x m 的二维矩阵，要求找到一个子矩阵，该子矩阵的行数和列数均为 x，并且其元素之和最大。
2. **前缀和矩阵的构建**：
   - 为了高效地计算任意 x x x 子矩阵的元素总和，首先构建一个前缀和矩阵 preSum。
   - `preSum[i][j]` 表示原矩阵中从 (0,0) 到 (i-1,j-1) 的所有元素之和。
   - 构建前缀矩阵的公式为：

    $$
    preSum[i][j] = preSum[i-1][j] + preSum[i][j-1] - preSum[i-1][j-1] + a[i-1][j-1]
    $$
   - 通过这种方式，可以在常数时间内计算任意子矩阵的和。
3. **遍历所有可能的 x x x 子矩阵**：
   - 遍历原矩阵中所有可能作为左上角起点的 (i,j)，使得子矩阵完全位于原矩阵内部，即满足 0 ≤ i ≤ n - x 和 0 ≤ j ≤ m - x。
   - 对于每一个起点 (i,j)，利用前缀和矩阵计算对应的子矩阵的元素总和：

    $$
    sum = preSum[i+x][j+x] - preSum[i+x][j] - preSum[i][j+x] + preSum[i][j]
    $$
   - 在遍历过程中，持续更新记录遇到的最大子矩阵和 maxSum。
4. **输出结果**：
   - 遍历完成后，maxSum 即为所有符合条件的 $x \cdot x $子矩阵中的最大元素和，输出该值作为最终结果。

### 代码

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main(){
    int n, m, x;
    cin >> n >> m >> x;
    vector<vector<int>> a(n, vector<int>(m, 0));

    //Input
    for(int i = 0; i < n; i++){
        for(int j = 0; j < m; j++){
            cin >> a[i][j];
        }
    }

    int maxSum =-1e9;

    // prefix_sum
    vector<vector<int>> preSum(n+1, vector<int>(m+1, 0));
    for(int i = 1; i <= n; i++){
        for(int j = 1; j <= m; j++){
            preSum[i][j] = preSum[i-1][j] + preSum[i][j-1] - preSum[i-1][j-1] + a[i-1][j-1];
        }
    }

    for(int i = 0; i <= n-x; i++){
        for(int j = 0; j <= m-x; j++){
            int sum = preSum[i+x][j+x] - preSum[i+x][j] - preSum[i][j+x] + preSum[i][j];
            maxSum = max(maxSum, sum);
        }
    }

    cout << maxSum << endl;
    return 0;
}
```

## Q2 女巫的魔法书

### 解题思路

1. 使用哈希表存储每个长度对应的词语及出现的次数。
2. 遍历文本的每个字符位置，i从0到M-1：
   1. 对每个词长度l，提取子字符串`sub=T[i-1+1:i]`。
   2. 检查`sub`是否在字典中，如果存在，则`totalMatches += 1`。

### 代码

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <unordered_map>

using namespace std;

int main(){
    ios::sync_with_stdio(false);
    cin.tie(0);
    
    int N, M;
    cin >> N >> M;

    vector<unordered_map<string,int>> d(51);
    string S;
    int l;
    for(int i = 0; i < N; i++){
        cin >> l>>S;
        d[l][S] += 1;
    }
    long long totalMatches = 0;
    string T;
    cin >> T;
    int textLength = T.length();
    for(int i = 0; i < textLength; i++){
        for(int j = 1; j <= 50; j++){
            if(i-j+1 < 0){
                continue;
            }
            string sub = T.substr(i-j+1, j);
            if(d[j].find(sub) != d[j].end()){
                totalMatches += d[j][sub];
            }
        }
    }
    cout << totalMatches;
    return 0;
}
```

## Q3 女巫的坩埚

### 解题思路

1. 本质上是区间覆盖问题；
2. 按照`tl`升序排列，如果`tl`相同，按照`tr`降序排列。
3. 贪心选择区间：
   1. 初始化cur_end=0，next_end=0，count=0；
   2. 选择能够覆盖当前未覆盖区间最宽的区间；
   3. 更新cur_end为该区间的右端点；
   4. 重复2、3直到`[1,N]`都被覆盖。

### 代码

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main(){
    int N, M;
    cin >> N >> M;

    vector<pair<int,int>> intervals(M);
    for(int i = 0; i < M; i++){
        cin >> intervals[i].first >> intervals[i].second;
    }
    // 按照tl升序排列，如果tl相同，按照tr降序排列
    sort(intervals.begin(), intervals.end(), [&](pair<int,int> &a, pair<int,int> &b)->bool {
        if(a.first != b.first){
            return a.first < b.first;
        }
        return a.second > b.second;
    });

    int cur_end = 0;
    int next_end = 0;
    int count = 0;
    int i = 0;

    while(cur_end < N){
        while(i < M && intervals[i].first <= cur_end+1){
            //找到可以覆盖当前区间的最宽的区间
            next_end=intervals[i].second>=next_end?intervals[i].second:next_end;
            i++;
        }
        if(next_end == cur_end){
            //找不到可以继续覆盖的区间
            break;
        }
        cur_end = next_end;
        count++;
    }
    
    if(cur_end>=N){
        //所有区间都被覆盖
        cout << count;
    }else{
        cout << -1;
    }

    return 0;
}
```

## Q4 女巫的背包1

### 解题思路

1. 0-1背包问题，使用二维数组`dp`来记录状态转移方程，`dp[j]`表示背包容量为j的时候获得的最大价值。
2. 对于物品i，从1到N遍历，保证每个物品只会选择一次；
3. 对于每个物品i，从C到wi递减遍历j，更新`dp[j]=max(dp[j],dp[j-wi]+vi])`;

### 代码

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main(){
    int N, C;
    cin >> N >> C;

    vector<pair<int,int>> items(N);
    for(int i = 0; i < N; i++){
        cin >> items[i].first >> items[i].second;
    }

    vector<int> dp(C+1);
    //遍历每个物品
    for(int i = 0; i < N; i++){
        int w=items[i].first;
        int v=items[i].second;
        //遍历容量
        for(int j = C; j >= w; j--){
            dp[j]=max(dp[j],dp[j-w]+v);
        }
    }

    cout << dp[C];

    return 0;
}
```

## Q5 女巫的背包2

### 解题思路

1. 类似上一题做法;
2. 对于每一种物品，对其数量进行二进制分解，得到的多个物品堆作为多个物品；

### 代码

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main(){
    int N, C;
    cin >> N >> C;

    vector<pair<int,int>> exp_items(N);
    // 将物品数量分解成多个物品
    for(int i = 0; i < N; i++){
        int k,w,v;
        cin >> k >> w >> v;

        int count = 1;
        while(k>0){
            if(k>=count){
                exp_items.emplace_back(make_pair(w*count,v*count));
                k-=count;
                count*=2;
            }else{
                exp_items.emplace_back(make_pair(w*k,v*k));
                k=0;
            }
        }
    }

    vector<int> dp(C+1);

    for(auto &item:exp_items){
        int w=item.first;
        int v=item.second;
        for(int j = C; j >= w; j--){
            dp[j]=max(dp[j],dp[j-w]+v);
        }
    }

    cout << dp[C];

    return 0;
}
```
