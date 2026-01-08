---
title: 记录一道XDOJ好题/一题复习C
date: 2026-01-08 15:46:17
tags: 
    - C++
    - XDOJ
categories: C++
cover: /images/C++/记录一道XDOJ好题_一题复习C/cover.png
---
## 前言
考试的时候看见这道题，气笑了。什么gal的存档点能有一万个?  
不过题目考察的东西其实相当全面，并且都不是很难，所以想再做一遍，复习+记录
## 题目描述
### 限制
时间限制: 1.0s  
内存限制：256.0MB  
### 题目内容
你正在玩一款 galgame  
  
这个 galgame 一共有 $n$ 个剧情节点，和 $m$ 位女主。  
  
对于第 $i$ 个剧情节点，共有 $k_i$ 个选项，分别通往第 $a_{i,j} (1 \le j \le k_i)$ 个剧情节点，且选择该选项可以为第 $b_{i,j}$ 位女主增加 $c_{i,j}$ 的好感度。当 $k_i$ 为 $0$ 时，表示该节点为结局。 
   
其中第 $1$ 个剧情节点为初始节点，不会从任何剧情节点进入，其余节点能且仅能从一个剧情节点进入。  
  
现在你有 $q$ 个存档，每个存档位于 $x$ 号剧情节点，希望能攻略 $y$ 号女主。  
求从当前节点开始，不断选择选项进入剧情节点，直到进入结局，最多能为 $y$ 号女主增加的好感度是多少。  
### 输入说明
第一行为两个正整数 $n, m$。  
后接若干组，按顺序表示每个剧情节点的信息： 
   
每组第一行为一个正整数 $k_i$，表示第 $i$ 个剧情节点有 $k_i$ 个选项。  
接下来 $k_i$ 行，每行三个整数，分别表示 $a_{i,j}, b_{i,j}, c_{i,j}$。  
  
后面一行为正整数 $q$，表示存档个数。  
  
接下来 $q$ 行，每行两个整数 $x, y$，表示当前在 $x$ 号节点，希望攻略 $y$ 号女主。
  
**输入样例：**  
```Text
5 2
2
2 1 5
3 1 10
1
4 1 10
1
5 1 5
0
0
2
1 1
2 1
```
### 输出说明
输出分为 $q$ 行，每行一个整数，表示能提升希望攻略角色的最大好感度。  
  
**输出样例：**
```Text
15
10
```
## 题目分析
对于这种又大又长的题目，我们肯定是要做**题目分析**的。  
### 内容分析
首先我们要学会像AI一样思考
1. 这道题核心是什么，考察的是什么
   - 题目要求我们计算一些数字，找到最大值
   - 根据它先后性质，我们可以想到树状动态规划，但是我们不会
   - 我们考虑放弃部分测试样点，遍历计算每一个路线，再找最大值。虽然慢，但是能做
2. 题目中有哪些数据结构，算法
   - 题目中有树状的存档，属于是N叉树
   - 树状遍历需要用到递归遍历每个存档
3. 题目输入数据的处理
   - 题目输入用了比较复杂的格式，我们需要用循环和数组来存储
  
现在，让我们计划一下要做的内容，分成一个个小项目
### 题目拆解
1. 选定树的结构，新建树的结构体，里面需要哪些内容？
   - 我们可以用下标来当作节点编号
   - 每个节点需要存储它的选项，选项可以用一个数组来存储
   - 每个选项要有哪些内容？是否需要写新的结构体
     - 1. 选项通往的节点编号
     - 2. 选项增加的好感度
     - 3. 选项对应的女主编号
   - 可能还需要一个二维数组来存存档信息
2. 读取输入数据，建立树的结构
   - 读取n和m
   - 循环读取每个节点的信息
   - 对于每个节点，读取它的选项数量k
   - 循环读取每个选项的信息，存储到节点的选项数组中
   - 读取存档数量q
   - 循环读取每个存档的信息，存储到二维数组中
3. 实现递归遍历函数，计算最大好感度
   - 定义一个递归函数，参数为当前节点编号和目标女主编号
4. 输出数据
根据以上的信息，我们已经可以生成一个大体的代码框架了
```C++
#include <iostream>
using namespace std;

typedef struct Option{
    //选项结构体
};

typedef struct Node{
    //节点结构体
};

//计算最大好感度的递归函数
int highest_affection(int node_index, int target);

int main(){
    //读取输入数据，建立树的结构

    //处理每个存档，调用递归函数计算最大好感度

    //输出结果
    return 0;
}

```
## 代码实现
有了上面的框架，我们一步步开始做。
### 节点和选项结构体
我们先写选项结构体  
选项有三个内容，都要写清楚
```C++
typedef struct Option{
    int to_node; //选项通往的节点编号
    int affection; //选项增加的好感度
    int target; //选项对应的女主编号
};
```

然后是节点结构体

!!! note 关于vector容器 

    停停停！  
    在写节点结构体之前，让我先介绍一个强大的东西：vector容器！  
    为什么要用vector？因为题目中每个节点的选项数量是不固定的，我们无法预先定义一个固定大小的数组来存储选项。  
    vector是C++标准库中的一个动态数组容器，可以根据需要动态调整大小，非常适合存储不定数量的数据。  
    使用vector非常简单，我们只需要包含头文件`<vector>`，然后就可以使用它了。  
    这里有几个常用的方法：  
      - `push_back(value)`：在vector的末尾添加一个元素
      - `size()`：返回vector中元素的数量
      - `[]`：通过下标访问元素
      - `clear()`：清空vector中的所有元素
      - `pop_back()`：删除vector末尾的元素
    还有很多可以自己了解..  
    用法示例：  
    ```C++
    #include <iostream>
    #include <vector>
    using namespace std;
    int main() {
        vector<int> numbers; // 创建一个存储整数的vector
        numbers.push_back(10); // 添加元素10
        numbers.push_back(20); // 添加元素20
        cout << numbers[0]; // 访问第一个元素，输出10
        cout << numbers.size(); // 输出vector的大小，输出2
    }
    ```


我寻思你应该看懂了，之后我会大量的用容器而不是数组。  
OK，回到节点结构体的编写  

```C++
...
#include <vector>
...
typedef struct Option{
    int to_node; //选项通往的节点编号
    int affection; //选项增加的好感度
    int target; //选项对应的女主编号
};
typedef struct Node{
    vector<Option> options; //节点的选项容器
};
```

### 读取输入数据，建立树的结构
接下来我们实现读取输入数据的部分

!!! note 关于cin和cout

    这就俩输入输出，和scanf与printf差不多，但是更方便一些。  
    大家不要学我，这样写程序会更慢，我只是懒得写那麽多了  
    示例：
    ```
    scanf("%d", &n);
    cin>>n;

    scanf("%d %d", &n, &m);
    cin>>n>>m;

    printf("%d\n", result);
    cout<<result<<endl;

    printf("%d %d\n", a, b);
    cout<<a<<" "<<b<<endl;
    ```

先做个大概框架，

```C++
int main(){
    // 变量初始化
    int m, n;      // n为剧情节点数，m为女主数
    cin >> n >> m; // 读取n和m

    // 一共要输入n个节点，所以先来个n次的循环
    for (int i = 0; i < n; i++)
    {
        // 输入选项数目k
        int k;
        cin >> k;
        // k个选项，进行k次循环读取
        for (int j = 0; j < k; j++)
        {
            // 读取每一个选项
        }
    }
    // 读取存档数q
    int q;
    cin >> q;
    // 初始化变量，是一个长度不限，宽度为2的二维数组容器
    vector<int[2]> maps;
    for (int i = 0; i < q; i++)
    {
        // 输入存档
    }
}
```

再详细填写

```C++
int main()
{
    // 读取输入数据，建立树的结构

    // 变量初始化
    int m, n;      // n为剧情节点数，m为女主数
    vector<Node> nodes;
    cin >> n >> m; // 读取n和m

    // 一共要输入n个节点，所以先来个n次的循环
    for (int i = 0; i < n; i++)
    {
        //初始化K和这个节点
        int k;
        struct Node node;
        // 输入选项数目k
        cin >> k;
        // k个选项，进行k次循环读取
        for (int j = 0; j < k; j++)
        {
            //初始化这个选项
            struct Option option;
            // 读取选项每一个值
            cin>>option.to_node>>option.target>>option.affection;
            //将这个选项赛季节点中
            node.options.push_back(option);
        }

        //将这个节点塞入节点容器中
        nodes.push_back(node);
    }
    // 读取存档数q
    vector<vector<int>> maps;
    for (int i = 0; i < q; i++)
    {
        //初始化一个存档
        //第一位为当前节点，第二位为目标女主
        int loc,target;
        cin>>loc>>target;
        // 插入存档
        maps.push_back({loc,target});
    }

    // 处理每个存档，调用递归函数计算最大好感度

    // 输出结果
    return 0;
}
```

### 实现递归遍历函数，计算最大好感度
接下来我们实现递归函数  
我们要搞清楚思路  
1. 递归的终止条件
   - 当前节点没有选项时，说明已经到达结局，返回0
2. 递归的参数和返回值
   - 参数：当前节点编号，目标女主编号
   - 返回值：从当前节点出发，能为目标女主增加的最大好感度
3. 递归的过程
   - 遍历当前节点的所有选项通往的节点，获取每个返回值。
   - 如果选项对应的女主是目标女主，则加上该选项的好感度
   - 返回最大好感度
```C++
//将节点容器声明为全局变量方便函数查找
vector<Node> nodes;
int main(){
    ...
}
//计算最大好感度的递归函数
int highest_affection(int node_index, int target){
    //先写终止条件
    if(nodes[node_index].options.size()==0){
        //当前节点没有选项，是结局，返回0
        return 0;
    }
    //初始化变量
    int maxAffection = 0;
    Node nodeCurrent = nodes[node_index];//取一下当前节点，方便后面写
    //遍历每个选项
    for(int i = 0;i<nodeCurrent.options.size();i++){
        //对每个选项所通往的节点递归调用这个函数
        int childAffection = highest_affection(nodeCurrent.options[i].to_node,target);
        //判断这个选项加不加好感
        if(nodeCurrent.options[i].target == target){
            childAffection+=nodeCurrent.options[i].affection;
        }
        //和最大好感度比较，保存更大的
        if(childAffection>maxAffection){
            maxAffection = childAffection;
        }
    }
    //返回最大好感度
    return maxAffection;
}
```

### 处理存档，输出结果
最后我们在主函数中处理存档，调用递归函数，并输出结果
```C++
    int sizeOfMaps = maps.size();
    for(int i = 0;i<sizeOfMaps;i++){
        cout<<highest_affection(maps[i][0],maps[i][1])<<endl;
    }
```

### 调试
我们在调试的过程中发现了数组越界，在容器Node中。  
我们回想，如果果节点编号是从1开始的，那么我们在访问nodes数组时就会越界。  
所以我们需要让编号=下标+1
```C++
int highest_affection(int node_index, int target){
    node_index--;
    //先写终止条件
    if(nodes[node_index].options.size()==0){
        //当前节点没有选项，是结局，返回0
        return 0;
    }
    ...
}
```

### 完整代码
```C++
#include <iostream>
#include <vector>
using namespace std;

typedef struct Option
{
    int to_node;   // 选项通往的节点编号
    int affection; // 选项增加的好感度
    int target;    // 选项对应的女主编号
};
typedef struct Node
{
    vector<Option> options; // 节点的选项容器
};

//将节点容器声明为全局变量方便函数查找
vector<Node> nodes;

// 计算最大好感度的递归函数
int highest_affection(int node_index, int target);

int main()
{
    // 读取输入数据，建立树的结构

    // 变量初始化
    int m, n;      // n为剧情节点数，m为女主数

    cin >> n >> m; // 读取n和m

    // 一共要输入n个节点，所以先来个n次的循环
    for (int i = 0; i < n; i++)
    {
        //初始化K和这个节点
        int k;
        struct Node node;
        // 输入选项数目k
        cin >> k;
        // k个选项，进行k次循环读取
        for (int j = 0; j < k; j++)
        {
            //初始化这个选项
            struct Option option;
            // 读取选项每一个值
            cin>>option.to_node>>option.target>>option.affection;
            //将这个选项赛季节点中
            node.options.push_back(option);
        }

        //将这个节点塞入节点容器中
        nodes.push_back(node);
    }
    // 读取存档数q
    int q;
    cin >> q;
    // 初始化变量，是一个长度不限，宽度为2的二维数组容器
    vector<vector<int>> maps;
    for (int i = 0; i < q; i++)
    {
        //初始化一个存档
        //第一位为当前节点，第二位为目标女主
        int loc,target;
        cin>>loc>>target;
        // 插入存档
        maps.push_back({loc,target});
    }

    // 处理每个存档，调用递归函数计算最大好感度
    int sizeOfMaps = maps.size();
    for(int i = 0;i<sizeOfMaps;i++){
        cout<<highest_affection(maps[i][0],maps[i][1])<<endl;
    }
    // 输出结果
    return 0;
}

int highest_affection(int node_index, int target){
    node_index--;
    //先写终止条件
    if(nodes[node_index].options.size()==0){
        //当前节点没有选项，是结局，返回0
        return 0;
    }
    //初始化变量
    int maxAffection = 0;
    Node nodeCurrent = nodes[node_index];//取一下当前节点，方便后面写
    //遍历每个选项
    for(int i = 0;i<nodeCurrent.options.size();i++){
        //对每个选项所通往的节点递归调用这个函数
        int childAffection = highest_affection(nodeCurrent.options[i].to_node,target);
        //判断这个选项加不加好感
        if(nodeCurrent.options[i].target == target){
            childAffection+=nodeCurrent.options[i].affection;
        }
        //和最大好感度比较，保存更大的
        if(childAffection>maxAffection){
            maxAffection = childAffection;
        }
    }
    //返回最大好感度
    return maxAffection;
}
```
## 结语
这道题目虽然看起来复杂，但是通过合理的分析和拆解，我们可以一步步实现它。  
希望这篇文章能帮助大家更好地理解如何处理复杂的题目。  
祝大家C语言考试顺利！