Absolutely. Here's your **DP Lab Test C++ Cheat Sheet**. I've structured it as a coding reference rather than a theoretical textbook. Focus on the state definitions, base cases, transitions, loop directions and code templates.

Everything is in your preferred C++ style: compact syntax, same-line braces, recursion first.

# **DP LAB TEST – C++ QUICK CHEAT SHEET**

# **DP revision map**

| Pattern      | State          |
| ------------ | -------------- |
| 1D DP        | `dp[i]`        |
| Knapsack     | `dp[i][w]`     |
| LCS          | `dp[i][j]`     |
| LIS          | `dp[i]`        |
| MCM          | `dp[i][j]`     |
| Partition DP | `dp[i][j]`     |
| Counting DP  | Number of ways |

**Three things to remember:**

- `min()` → optimization/minimum cost.
- `max()` → optimization/maximum profit.
- `+` and `*` → counting combinations of possibilities.

# **1. Basic DP**

### **Fibonacci**

\\[ dp[i]=dp[i-1]+dp[i-2] \\]

```
int fib(int n){
    if(n<=1)return n;

    int a=0,b=1,c;

    for(int i=2;i<=n;i++){
        c=a+b;
        a=b;
        b=c;
    }

    return b;
}
```

### **Climbing Stairs**

Reach stair `n`, taking either 1 or 2 steps.

\\[ dp[i]=dp[i-1]+dp[i-2] \\]

```
int climbStairs(int n){
    int dp[n+1];
    dp[0]=1;
    dp[1]=1;

    for(int i=2;i<=n;i++)
        dp[i]=dp[i-1]+dp[i-2];

    return dp[n];
}
```

### **Min Cost Climbing Stairs**

\\[ dp[i]=cost[i]+\min(dp[i-1],dp[i-2]) \\]

```
int minCostClimbingStairs(vector<int>& a){
    int n=a.size();
    vector<int>dp(n+1,0);

    for(int i=2;i<=n;i++)
        dp[i]=min(dp[i-1]+a[i-1],dp[i-2]+a[i-2]);

    return dp[n];
}
```

### **House Robber**

Cannot take adjacent elements.

\\[ dp[i]=\max(dp[i-1],a[i]+dp[i-2]) \\]

```
int rob(vector<int>& a){
    int n=a.size();
    if(n==0)return 0;
    if(n==1)return a[0];

    int prev2=0,prev1=0;

    for(int i=0;i<n;i++){
        int cur=max(prev1,a[i]+prev2);
        prev2=prev1;
        prev1=cur;
    }

    return prev1;
}
```

# **2. Knapsack DP / Rod Cutting**

Very important

### **0/1 Knapsack**

Each item can be selected at most once.

\\[ dp[i][w]=\max(dp[i-1][w],val[i-1]+dp[i-1][w-wt[i-1]]) \\]

```
int knapSack(int W,vector<int>& wt,vector<int>& val,int n){
    vector<vector<int>>dp(n+1,vector<int>(W+1,0));

    for(int i=1;i<=n;i++){
        for(int w=0;w<=W;w++){
            dp[i][w]=dp[i-1][w];

            if(w>=wt[i-1])
                dp[i][w]=max(dp[i][w],
                    val[i-1]+dp[i-1][w-wt[i-1]]);
        }
    }

    return dp[n][W];
}
```

### **Space optimized 0/1 Knapsack**

```
vector<int>dp(W+1,0);

for(int i=0;i<n;i++){
    for(int w=W;w>=wt[i];w--){
        dp[w]=max(dp[w],val[i]+dp[w-wt[i]]);
    }
}
```



Remember

&#x20;Iterate backwards to prevent reusing the same item.



### **Unbounded Knapsack**

Items can be used unlimited times.

```
vector<int>dp(W+1,0);

for(int i=0;i<n;i++){
    for(int w=wt[i];w<=W;w++){
        dp[w]=max(dp[w],val[i]+dp[w-wt[i]]);
    }
}
```



Remember

&#x20;Iterate forwards to allow reuse of the same item.



### **Rod Cutting**

Given price of each length, maximize revenue for rod length `n`.

```
int cutRod(vector<int>& price){
    int n=price.size();
    vector<int>dp(n+1,0);

    for(int i=1;i<=n;i++){
        for(int j=1;j<=i;j++){
            dp[i]=max(dp[i],price[j-1]+dp[i-j]);
        }
    }

    return dp[n];
}
```

### **Coin Change – Minimum Coins**

```
int coinChange(vector<int>& coins,int amount){
    const int INF=1e9;
    vector<int>dp(amount+1,INF);
    dp[0]=0;

    for(int i=1;i<=amount;i++){
        for(int c:coins){
            if(i>=c)
                dp[i]=min(dp[i],1+dp[i-c]);
        }
    }

    return dp[amount]==INF?-1:dp[amount];
}
```

### **Coin Change II – Number of Ways**

```
int change(int amount,vector<int>& coins){
    vector<int>dp(amount+1,0);
    dp[0]=1;

    for(int c:coins){
        for(int j=c;j<=amount;j++){
            dp[j]+=dp[j-c];
        }
    }

    return dp[amount];
}
```

**Difference:** Minimum coins uses `min()`, while Coin Change II uses addition to count ways.

# **3. Matrix Chain Multiplication (MCM)**

Very important

### **Recursive template**

```
int help(vector<int>& a,int i,int j){
    if(i+1==j)return 0;

    int ans=INT_MAX;

    for(int k=i+1;k<j;k++){
        int cost=a[i]*a[k]*a[j]
                +help(a,i,k)
                +help(a,k,j);

        ans=min(ans,cost);
    }

    return ans;
}
```

### **Memoization**

```
int dp[101][101];

int help(vector<int>& a,int i,int j){
    if(i+1==j)return 0;

    if(dp[i][j]!=-1)return dp[i][j];

    int ans=INT_MAX;

    for(int k=i+1;k<j;k++){
        int cost=a[i]*a[k]*a[j]
                +help(a,i,k)
                +help(a,k,j);

        ans=min(ans,cost);
    }

    return dp[i][j]=ans;
}
```

Initialize:

```
memset(dp,-1,sizeof(dp));
```

### **Tabulation**

```
int n=arr.size();
int dp[101][101]={0};

for(int len=2;len<n;len++){
    for(int i=0;i+len<n;i++){
        int j=i+len;
        dp[i][j]=INT_MAX;

        for(int k=i+1;k<j;k++){
            int cost=arr[i]*arr[k]*arr[j]
                    +dp[i][k]+dp[k][j];

            dp[i][j]=min(dp[i][j],cost);
        }
    }
}

return dp[0][n-1];
```

**MCM loop order:**

1. Increasing interval length.
2. Increasing starting index.
3. Try every partition point.

# **4. Interval DP Variations**

### **A. Minimum Cost to Cut a Stick**

```
int help(vector<int>& a,int i,int j){
    if(i+1==j)return 0;

    int ans=INT_MAX;

    for(int k=i+1;k<j;k++){
        int cost=a[j]-a[i]
                +help(a,i,k)
                +help(a,k,j);

        ans=min(ans,cost);
    }

    return ans;
}
```

Preprocessing:

```
sort(cuts.begin(),cuts.end());
cuts.insert(cuts.begin(),0);
cuts.push_back(n);
```

### **B. Burst Balloons**

```
int help(vector<int>& a,int i,int j){
    if(i+1==j)return 0;

    int ans=0;

    for(int k=i+1;k<j;k++){
        int cost=a[i]*a[k]*a[j]
                +help(a,i,k)
                +help(a,k,j);

        ans=max(ans,cost);
    }

    return ans;
}
```

Preprocessing:

```
a.insert(a.begin(),1);
a.push_back(1);
```

**Important:** Choose the LAST balloon to burst.

### **C. Palindrome Partitioning II**

```
bool isPal(string& s,int i,int j){
    while(i<j){
        if(s[i]!=s[j])return false;
        i++;
        j--;
    }
    return true;
}

int help(string& s,int i){
    if(i==s.size())return -1;

    int ans=INT_MAX;

    for(int j=i;j<s.size();j++){
        if(isPal(s,i,j)){
            ans=min(ans,1+help(s,j+1));
        }
    }

    return ans;
}
```

Here the state is `help(i)` rather than `help(i,j)`.

### **D. Boolean Parenthesization**

Maintain two states:

- `T` = number of ways to evaluate TRUE.
- `F` = number of ways to evaluate FALSE.

For every operator:

| Operator | TRUE                | FALSE               |
| -------- | ------------------- | ------------------- |
| AND      | `LT*RT`             | `LT*RF+LF*RT+LF*RF` |
| OR       | `LT*RT+LT*RF+LF*RT` | `LF*RF`             |
| XOR      | `LT*RF+LF*RT`       | `LT*RT+LF*RF`       |

Use modulo:

```
const int MOD=1e9+7;
```

# **5. Longest Common Subsequence (LCS)**

Very important

### **LCS – Tabulation**

```
int longestCommonSubsequence(string s1,string s2){
    int n=s1.size(),m=s2.size();

    vector<vector<int>>dp(n+1,vector<int>(m+1,0));

    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            if(s1[i-1]==s2[j-1])
                dp[i][j]=1+dp[i-1][j-1];
            else
                dp[i][j]=max(dp[i-1][j],dp[i][j-1]);
        }
    }

    return dp[n][m];
}
```

### **Print LCS**

After computing the table:

```
string ans="";
int i=n,j=m;

while(i>0 && j>0){
    if(s1[i-1]==s2[j-1]){
        ans+=s1[i-1];
        i--;
        j--;
    }
    else if(dp[i-1][j]>dp[i][j-1]){
        i--;
    }
    else{
        j--;
    }
}

reverse(ans.begin(),ans.end());
```

### **Longest Common Substring**

Difference: On mismatch, set `dp[i][j]=0`, not maximum of previous states.

```
if(s1[i-1]==s2[j-1])
    dp[i][j]=1+dp[i-1][j-1];
else
    dp[i][j]=0;

ans=max(ans,dp[i][j]);
```

### **Edit Distance**

Operations: Insert, Delete, Replace.

```
if(s1[i-1]==s2[j-1])
    dp[i][j]=dp[i-1][j-1];
else
    dp[i][j]=1+min({
        dp[i-1][j],    // Delete
        dp[i][j-1],    // Insert
        dp[i-1][j-1]   // Replace
    });
```

Base cases:

```
dp[i][0]=i;
dp[0][j]=j;
```

### **Shortest Common Supersequence – Length**

\\[ SCS=n+m-LCS \\]

```
return n+m-lcs;
```

### **Minimum Insertions/Deletions to Convert String A to B**

```
int lcs=...;

int deletions=n-lcs;
int insertions=m-lcs;

return deletions+insertions;
```

### **Longest Palindromic Subsequence**

```
reverse(s.begin(),s.end());
return LCS(s,reverse_s);
```

# **6. Longest Increasing Subsequence (LIS)**

Very important

### **LIS – O(n²)**

```
int lengthOfLIS(vector<int>& a){
    int n=a.size();
    vector<int>dp(n,1);

    int ans=1;

    for(int i=0;i<n;i++){
        for(int j=0;j<i;j++){
            if(a[j]<a[i]){
                dp[i]=max(dp[i],1+dp[j]);
            }
        }

        ans=max(ans,dp[i]);
    }

    return ans;
}
```

State:

\\[ dp[i]=\text{LIS ending at index }i \\]

### **LIS – O(n log n)**

```
int lengthOfLIS(vector<int>& a){
    vector<int>temp;

    for(int x:a){
        auto it=lower_bound(temp.begin(),temp.end(),x);

        if(it==temp.end())
            temp.push_back(x);
        else
            *it=x;
    }

    return temp.size();
}
```

**Remember:**

- `lower_bound()` → strictly increasing.
- `upper_bound()` → non-decreasing.

### **Maximum Sum Increasing Subsequence**

```
vector<int>dp=a;

for(int i=0;i<n;i++){
    for(int j=0;j<i;j++){
        if(a[j]<a[i]){
            dp[i]=max(dp[i],a[i]+dp[j]);
        }
    }
}

return *max_element(dp.begin(),dp.end());
```

### **Number of Longest Increasing Subsequences**

```
vector<int>len(n,1),cnt(n,1);
int maxi=1;

for(int i=0;i<n;i++){
    for(int j=0;j<i;j++){
        if(a[j]<a[i]){
            if(len[j]+1>len[i]){
                len[i]=len[j]+1;
                cnt[i]=cnt[j];
            }
            else if(len[j]+1==len[i]){
                cnt[i]+=cnt[j];
            }
        }
    }

    maxi=max(maxi,len[i]);
}

int ans=0;

for(int i=0;i<n;i++){
    if(len[i]==maxi)
        ans+=cnt[i];
}

return ans;
```

# **7. Multidimensional Arrays – Pass by Reference**

Since you specifically asked about this, keep these templates handy.

### **Fixed dimensions**

```
void func(int (&a)[3][4]){
    a[0][0]=100;
}
```

### **Variable dimensions using templates**

```
template<int N,int M>
void func(int (&a)[N][M]){
    for(int i=0;i<N;i++){
        for(int j=0;j<M;j++){
            cout<<a[i][j]<<" ";
        }
    }
}
```

### **3D array**

```
template<int N,int M,int P>
void func(int (&a)[N][M][P]){
    for(int i=0;i<N;i++){
        for(int j=0;j<M;j++){
            for(int k=0;k<P;k++){
                cout<<a[i][j][k]<<" ";
            }
        }
    }
}
```

Remember: dimensions must match the actual array type.

# **8. Complexity Cheat Sheet**

| Problem                  | Time             | Space        |
| ------------------------ | ---------------- | ------------ |
| Fibonacci                | \\(O(n)\\)       | \\(O(1)\\)   |
| Climbing Stairs          | \\(O(n)\\)       | \\(O(1)\\)   |
| House Robber             | \\(O(n)\\)       | \\(O(1)\\)   |
| 0/1 Knapsack             | \\(O(nW)\\)      | \\(O(W)\\)\* |
| Unbounded Knapsack       | \\(O(nW)\\)      | \\(O(W)\\)   |
| Rod Cutting              | \\(O(n^2)\\)     | \\(O(n)\\)   |
| MCM                      | \\(O(n^3)\\)     | \\(O(n^2)\\) |
| Stick Cutting            | \\(O(C^3)\\)     | \\(O(C^2)\\) |
| Burst Balloons           | \\(O(n^3)\\)     | \\(O(n^2)\\) |
| LCS                      | \\(O(nm)\\)      | \\(O(nm)\\)  |
| Longest Common Substring | \\(O(nm)\\)      | \\(O(nm)\\)  |
| Edit Distance            | \\(O(nm)\\)      | \\(O(nm)\\)  |
| LIS                      | \\(O(n^2)\\)     | \\(O(n)\\)   |
| LIS Binary Search        | \\(O(n\log n)\\) | \\(O(n)\\)   |
| Boolean Parenthesization | \\(O(n^3)\\)     | \\(O(n^2)\\) |

*For space-optimized 0/1 Knapsack.*

# **9. Common mistakes that cost marks**

**Watch these carefully**

| Mistake                          | Correct approach                       |
| -------------------------------- | -------------------------------------- |
| 0/1 Knapsack forward loop        | Backward loop                          |
| Unbounded Knapsack backward loop | Forward loop                           |
| LCS mismatch                     | `max(top,left)`                        |
| Common Substring mismatch        | `0`                                    |
| LIS condition                    | `<` for strict LIS                     |
| MCM base case                    | `i+1==j` for your indexing             |
| Burst Balloons                   | Choose last balloon                    |
| Stick Cutting                    | Add boundaries and sort                |
| Edit Distance base cases         | `dp[i][0]=i`, `dp[0][j]=j`             |
| Counting DP                      | Initialize `dp[0]=1` where appropriate |
| Memoization                      | Check cache before recursion           |
| Large costs                      | Use `long long`                        |

# **10. Universal DP coding workflow**

When you receive an unseen problem tomorrow, follow this exact process:

```
// STEP 1: Define the state
// What does help(i) or help(i,j) mean?

// STEP 2: Write the base case
if(base_condition)
    return base_answer;

// STEP 3: Check memoization
if(dp[i][j]!=-1)
    return dp[i][j];

// STEP 4: Initialize answer
int ans=INT_MAX; // Minimum
// int ans=INT_MIN; // Maximum
// int ans=0;       // Counting

// STEP 5: Explore choices
for(int k=...;k<...;k++){
    int cost=...;
    ans=min(ans,cost);
}

// STEP 6: Return and store
return dp[i][j]=ans;
```

For tabulation:

```
// 1. Initialize dp table
// 2. Set base cases
// 3. Determine dependency order
// 4. Fill smaller states before larger states
// 5. Return final state
```

# **Final 5-minute revision**

If you remember only these distinctions, you'll avoid many common errors:

**Knapsack**

- 0/1 → backward loop.
- Unbounded → forward loop.

**String DP**

- LCS → mismatch takes maximum.
- Common Substring → mismatch resets to zero.
- Edit Distance → minimum of three operations.

**Sequence DP**

- LIS → previous smaller elements.
- Print LIS → maintain parent indices.
- Number of LIS → maintain length and count.

**Interval DP**

- MCM → minimize.
- Burst Balloons → maximize.
- Stick Cutting → minimize.
- Boolean Parenthesization → count.

**C++**

- Initialize DP correctly.
- Watch array indices.
- Use `long long` for large values.
- Check empty and single-element inputs.

My strongest suggestion: spend your final revision time writing MCM, Rod Cutting, LCS, LIS and Coin Change from memory. Those five templates cover a huge portion of the patterns in your syllabus.

Good luck with the lab test! You've got this. 💪
