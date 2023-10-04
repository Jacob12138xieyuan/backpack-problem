# backpack-problem
### 01背包问题
背包容量为m=10，物品数量为n=4，每个物品只有一个
物品重量weights=[2,3,4,7], 价值values=[1,3,5,9]
求背包最大能装物品的最大价值为多少
```
# 动态规划，01背包问题，dp[i][j]表示前i个物品，背包容量j能装的最大价值为dp[i][j]
# 状态转移方程，如果第i个物品重量weights[i]大于背包容量j，根本不用考虑装第i个物品，
# 前i个物品，背包容量j能装的最大价值为dp[i][j]等价于前i-1个物品中选的最大价值dp[i-1][j]
# 如果第i个物品重量小于背包容量，需要考虑装还是不装第i个物品，
# 因为装会增加价值values[i],但会牺牲背包容量weights[i]，从而可能导致之前更大价值的物品不能装入背包
# 等价于前i-1个物品，背包容量j-weights[i]的最大价值dp[i-1][j-weights[i]]+values[i]
# 写法1：二维数组
def max_value(m, weights, values):
    n = len(weights)
    weights = [0] + weights
    values = [0] + values
    dp = [[0 for _ in range(m+1)] for _ in range(n+1)]
    # 第1行和第1列最大重量都是0，因为没有物品或者没有容量
    for i in range(1, n+1):
        for j in range(1, m+1):
            # if j < weights[i]:
            #     dp[i][j] = dp[i-1][j]
            # else:
            #     dp[i][j] = max(dp[i-1][j], dp[i-1][j-weights[i]]+values[i])
            # 或者写成
            dp[i][j] = dp[i-1][j]
            if j >= weights[i]:
                dp[i][j] = max(dp[i][j], dp[i - 1][j - weights[i]] + values[i])
    print(dp)
    # [[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
    #  [0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1],
    #  [0, 0, 1, 3, 3, 4, 4, 4, 4, 4, 4],
    #  [0, 0, 1, 3, 6, 6, 7, 9, 9, 10, 10],
    #  [0, 0, 1, 3, 6, 6, 7, 9, 10, 10, 11]]
    return dp[-1][-1]


# 写法2：一维滚动数组
#      j 0  1  2  3  4  5  6  7  8  9  10
#  i   [[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
# 1(2)  [0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1],
# 2(3)  [0, 0, 1, 3, 3, 4, 4, 4, 4, 4, 4],
# 3(4)  [0, 0, 1, 3, 6, 6, 7, 9, 9, 10, 10],
# 4(7)  [0, 0, 1, 3, 6, 6, 7, 9, 10, 10, 11]]
# 根据观察，当j<weights[i]时，dp[i][j]都是继承上一格
# 状态转移方程max(dp[i-1][j], dp[i-1][j-weights[i]]+values[i])
# 当前状态只取决于上一行左边的状态，所以只需要遍历j>=weights[i]的情况
# 并且需要从右边开始遍历，因为左边的值相当于“上一行”的值，需要参考从而得到dp[j]
def max_value(m, weights, values):
    n = len(weights)
    weights = [0] + weights
    values = [0] + values
    dp = [0 for _ in range(m+1)]
    # 第1行和第1列最大重量都是0，因为没有物品或者没有容量
    for i in range(1, n+1):
        for j in range(m, weights[i]-1, -1):  # 左闭右开
            dp[j] = max(dp[j], dp[j-weights[i]]+values[i])
        print(dp)
        # [0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1]
        # [0, 0, 1, 3, 3, 4, 4, 4, 4, 4, 4]
        # [0, 0, 1, 3, 6, 6, 7, 9, 9, 10, 10]
        # [0, 0, 1, 3, 6, 6, 7, 9, 10, 10, 11]
    return dp[-1]

capacity = 10
weights = [2, 3, 4, 8]
values = [1, 3, 6, 10]
max_ = max_value(capacity, weights, values)
```

### 完全背包问题
背包容量为m=10，物品数量为n=4，每个物品有无限个
物品重量weights=[2,3,4,7], 价值values=[1,3,5,9]
求背包最大能装物品的最大价值为多少
```
# 完全背包，每个物品有无限个，比如i=1，j=4，可以放两个物品1，价值为2，
# 所以要在同层左边或者上一层左边的基础上叠加最大价值，
# 其实不用考虑上一层左边dp[i-1][j - weights[i]] + values[i]因为
# dp[i-1][j - weights[i]] + values[i] <= dp[i][j - weights[i]] + values[i]
# 01背包问题中，只有一个物品，只能往上一层左边的基础上叠加
# 写法1：二维数组
def max_value(m, weights, values):
    n = len(weights)
    weights = [0] + weights
    values = [0] + values
    dp = [[0 for _ in range(m+1)] for _ in range(n+1)]
    # 第1行和第1列最大重量都是0，因为没有物品或者没有容量
    for i in range(1, n+1):
        for j in range(1, m+1):
            dp[i][j] = dp[i - 1][j]
            if j >= weights[i]:
                dp[i][j] = max(dp[i][j], dp[i][j - weights[i]] + values[i])
    print(dp)
    # [[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
    #  [0, 0, 1, 1, 2, 2, 3, 3, 4, 4, 5],
    #  [0, 0, 1, 3, 3, 4, 6, 6, 7, 9, 9],
    #  [0, 0, 1, 3, 6, 6, 7, 9, 12, 12, 13],
    #  [0, 0, 1, 3, 6, 6, 7, 9, 12, 12, 13]]
    return dp[-1][-1]

# 写法2：一维滚动数组
#     j 0  1  2  3  4  5  6  7  8  9  10
#  i  [[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
# 1(2) [0, 0, 1, 1, 2, 2, 3, 3, 4, 4, 5],
# 2(3) [0, 0, 1, 3, 3, 4, 6, 6, 7, 9, 9],
# 3(4) [0, 0, 1, 3, 6, 6, 7, 9, 12, 12, 13],
# 4(8) [0, 0, 1, 3, 6, 6, 7, 9, 12, 12, 13]]
# 根据观察发现j < weights[i]时，dp[i][j]都来自上一格，不用改变
# 当前j依赖于左边dp[j],所以从左往右遍历，这是与01背包一维数组写法唯一的区别
def max_value(m, weights, values):
    n = len(weights)
    weights = [0] + weights
    values = [0] + values
    dp = [0 for _ in range(m+1)]
    # 第1行和第1列最大重量都是0，因为没有物品或者没有容量
    for i in range(1, n+1):
        for j in range(weights[i], m+1):
            dp[j] = max(dp[j], dp[j - weights[i]] + values[i])
        print(dp)
        # [0, 0, 1, 1, 2, 2, 3, 3, 4, 4, 5]
        # [0, 0, 1, 3, 3, 4, 6, 6, 7, 9, 9]
        # [0, 0, 1, 3, 6, 6, 7, 9, 12, 12, 13]
        # [0, 0, 1, 3, 6, 6, 7, 9, 12, 12, 13]
    return dp[-1]

capacity = 10
weights = [2, 3, 4, 8]
values = [1, 3, 6, 10]
max_ = max_value(capacity, weights, values)
```

