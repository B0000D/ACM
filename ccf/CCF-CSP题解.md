# CCF-CSP题解

田鑫

标签（空格分隔）： ccf

---

# CCF-CSP  201312-4 有趣的数

## 1、 题目

**问题描述**

​	我们把一个数称为有趣的，当且仅当：

1. 它的数字只包含0, 1, 2, 3，且这四个数字都出现过至少一次。
2. 所有的0都出现在所有的1之前，而所有的2都出现在所有的3之前。
3. 最高位数字不为0。

　　因此，符合我们定义的最小的有趣的数是2013。除此以外，4位的有趣的数还有两个：2031和2301。
　　请计算恰好有n位的有趣的数的个数。由于答案可能非常大，只需要输出答案除以1000000007的余数。

**输入格式**

​	输入只有一行，包括恰好一个正整数n $(4 ≤ n ≤ 1000)$。

**输出格式**

​	输出只有一行，包括恰好n 位的整数中有趣的数的个数除以1000000007的余数。

**样例输入**

​	4

**样例输出**

​	3

## 2、 题解

​    该题为**多阶段决策过程的最优化**问题。

​    在现实生活中，有一类活动的过程，由于它的特殊性，可将过程分成若干个互相联系的阶段，在它的每一阶段都需要作出决策，从而使整个过程达到最好的活动效果。当然，各个阶段决策的选取不是任意确定的，它**依赖于当前面临的状态，又影响以后的发展**，当各个阶段决策确定后，就组成一个决策序列，因而也就确定了整个过程的一条活动路线，这种把一个问题看作是一个前后关联具有链状结构的多阶段过程就称为多阶段决策过程，这种问题就称为多阶段决策问题。

​    该类问题用**动态规划**算法解决，含有**递推**的思想以及各种数学原理。把多阶段过程转化为一系列单阶段问题，利用各阶段之间的关系，逐个求解。

不考虑条件1，共有六种合法状态：

1. 只含2
2. 只含2、0
3. 只含2、3
4. 只含2、0、1
5. 只含2、0、3
6. 含4种数字。

用$f[i][0]~f[i][5]$来分别表示长度为i的整数，满足上述状态的个数。
那么可以得到状态转移方程：

1. $f[i][0]=1$，位数为i且只含2的整数有且只有1个
2. $f[i][1]=2*f[i-1][1]+f[i-1][0]$，位数为i且只含2、0的整数可以由位数为$i-1$的只含2、0的整数通过在末尾添加0或者2得到，也可以由位数为i-1的只含2的整数在末尾添加0得到。

其余的状态转移方程以此类推。

## 3、 程序

```c++
#include <iostream>
#include <cstdio>
using namespace std;  
typedef long long LL;  
#define mod 1000000007  

int n;
LL f[1001][6];  

void work(){
	int i;
    f[1][0]=1;
    for(i=2;i<=n;++i){  
        f[i][0]=1;//只含2  
        f[i][1]=(2*f[i-1][1]%mod+f[i-1][0])%mod;//只含2、0  末尾0或2、末尾0  
        f[i][2]=(f[i-1][2]+f[i-1][0])%mod;//只含2、3 末尾3  
        f[i][3]=(2*f[i-1][3]%mod+f[i-1][1])%mod;//只含2、0、1 末尾2或1、末尾1  
        f[i][4]=((2*f[i-1][4]%mod+f[i-1][2])%mod+f[i-1][1])%mod;//只含2、0、3 末尾1或3、末尾0、末尾3  
        f[i][5]=((2*f[i-1][5]%mod+f[i-1][4])%mod+f[i-1][3])%mod;//含4个数字  末尾1或3  
    }  
    cout<<f[n][5]<<endl;
}  
  
int main(){  
    cin>>n;
    work ();
    return 0;
} 
```



---

# CCF-CSP  201312-5 I'm stuck!

## 1、 题目

**问题描述**

　　给定一个R行C列的地图，地图的每一个方格可能是'#', '+', '-', '|', '.', 'S', 'T'七个字符中的一个，分别表示如下意思：
　　'#': 任何时候玩家都不能移动到此方格；
　　'+': 当玩家到达这一方格后，下一步可以向上下左右四个方向相邻的任意一个非'#'方格移动一格；
　　'-': 当玩家到达这一方格后，下一步可以向左右两个方向相邻的一个非'#'方格移动一格；
　　'|': 当玩家到达这一方格后，下一步可以向上下两个方向相邻的一个非'#'方格移动一格；
　　'.': 当玩家到达这一方格后，下一步只能向下移动一格。如果下面相邻的方格为'#'，则玩家不能再移动；
　　'S': 玩家的初始位置，地图中只会有一个初始位置。玩家到达这一方格后，下一步可以向上下左右四个方向相邻的任意一个非'#'方格移动一格；
　　'T': 玩家的目标位置，地图中只会有一个目标位置。玩家到达这一方格后，可以选择完成任务，也可以选择不完成任务继续移动。如果继续移动下一步可以向上下左右四个方向相邻的任意一个非'#'方格移动一格。
　　此外，玩家不能移动出地图。
　　请找出满足下面两个性质的方格个数：
　　1. 玩家可以从初始位置移动到此方格；
　　2. 玩家不可以从此方格移动到目标位置。

**输入格式**

　　输入的第一行包括两个整数R 和C，分别表示地图的行和列数。$(1 ≤ R, C ≤ 50)$。
　　接下来的R行每行都包含C个字符。它们表示地图的格子。地图上恰好有一个'S'和一个'T'。

**输出格式**

　　如果玩家在初始位置就已经不能到达终点了，就输出“I'm stuck!”（不含双引号）。否则的话，输出满足性质的方格的个数。

**样例输入**

​	5 5
​	--+-+
​	..|#.
​	..|##
​	S-+-T
​	\####.

**样例输出**

​	2

**样例说明**

　　如果把满足性质的方格在地图上用'X'标记出来的话，地图如下所示：
　　--+-+
　　..|#X
　　..|##
　　S-+-T
　　\####X

## 2、 题解

**搜索**

经典的搜索类问题，可以bfs，也可以dfs，下面详细讲解dfs做法。

1. 从起点点开始dfs，用viss\[x]\[y]数组表示能从起点走到(x,y)，例如：方格\[x]\[y]='|',则方格\[x]\[y]可以到达\[x+1]\[y]和\[x-1]\[y]；
2. 从终点开始dfs，用visd[x]\[y]数组表示能从(x,y)走到终点，注意判断条件与步骤一相反，例如：当方格\[x+1]\[y]、\[x-1]\[y]='|'时，可以到达点\[x]\[y]；
3. viss=1且visd=0的方格，即源点能到达且不能到达终点的方格数。

## 3、 程序

```c++
#include <iostream>  
#include <cstring>  
using namespace std;

const int N=57;
  
char map[N][N];
int ans,sx,sy,tx,ty,R,C,addx[5]={-1,1,0,0},addy[5]={0,0,1,-1};
bool viss[N][N],visd[N][N];

void init (){
    int i,j;
    cin>>R>>C;
    for(i=0;i<R;i++)
        cin>>map[i];
    for(i=0;i<R;i++)
        for(j=0;j<C;j++)
            if(map[i][j]=='S')
                sx=i,sy=j;
            else if(map[i][j]=='T')
                tx=i,ty=j;
} 

bool ok1(int x,int y){
    if (0<=x&&x<R&&0<=y&&y<C&&!viss[x][y]&&map[x][y]!='#')  
        return true;
    else
        return false;
}  

bool ok2(int x,int y){
    if (0<=x&&x<R&&0<=y&&y<C&&!visd[x][y]&&map[x][y]!='#')  
        return true;
    else
        return false;
}  

void dfss(int x,int y){
    int i,tmpx,tmpy;
    viss[x][y]=1;
    if (map[x][y]=='+'||map[x][y]=='S'||map[x][y]=='T'){
        for (i=0;i<4;i++){
            tmpx=x+addx[i];
            tmpy=y+addy[i];
            if (ok1(tmpx,tmpy))  
                dfss(tmpx,tmpy);
        }  
    }else if(map[x][y]=='-'){
        for(i=2;i<=3;i++){
            tmpx=x+addx[i];
            tmpy=y+addy[i];
            if(ok1(tmpx,tmpy))  
                dfss(tmpx,tmpy);
        }  
    }else if(map[x][y]=='|'){
        for(i=0;i<=1;i++){
            tmpx=x+addx[i];
            tmpy=y+addy[i];
            if(ok1(tmpx,tmpy))  
                dfss(tmpx,tmpy);
        }  
    }else if(map[x][y]=='.'){
            tmpx=x+1;
        	tmpy=y;
        	if(ok1(tmpx,tmpy))  
            	dfss(tmpx,tmpy);
    }  
}  

void dfsd(int x,int y){
    int i,tmpx,tmpy;
    visd[x][y]=1;
    tmpx=x+addx[0];
    tmpy=y+addy[0];
    if (ok2(tmpx,tmpy)&&map[tmpx][tmpy]!='-'&&map[tmpx][tmpy]!='#')
        dfsd(tmpx,tmpy);
    tmpx=x+addx[1];
    tmpy=y+addy[1];
    if (ok2(tmpx,tmpy)&&map[tmpx][tmpy]!='-'&&map[tmpx][tmpy]!='#'&&map[tmpx][tmpy]!='.')
        dfsd(tmpx,tmpy);
    tmpx=x+addx[2];
    tmpy=y+addy[2];
    if (ok2(tmpx,tmpy)&&map[tmpx][tmpy]!='|'&&map[tmpx][tmpy]!='#'&&map[tmpx][tmpy]!='.')
        dfsd(tmpx,tmpy);
    tmpx=x+addx[3];
    tmpy=y+addy[3];
    if (ok2(tmpx,tmpy)&&map[tmpx][tmpy]!='|'&&map[tmpx][tmpy]!='#'&&map[tmpx][tmpy]!='.')
        dfsd(tmpx,tmpy);
} 

void work (){
	int i,j;
    dfss (sx,sy);
    dfsd (tx,ty);
    if (!viss[tx][ty]){
        cout<<"I'm stuck!"<<endl;
        return;
    }
    for (i=0;i<R;i++){
        for (j=0;j<C;j++){
            if (viss[i][j]&&!visd[i][j])
                ans++;
        }
    }
    cout<<ans<<endl;
}

int main(){
    init ();
    work ();
    return 0;
}
```

---

# CCF-CSP 201403-4	无线网络

## 1、 题目

**问题描述**　　

​	目前在一个很大的平面房间里有 n 个无线路由器,每个无线路由器都固定在某个点上。任何两个无线路由器只要距离不超过 r 就能互相建立网络连接。
　　除此以外,另有 m 个可以摆放无线路由器的位置。你可以在这些位置中选择至多 k 个增设新的路由器。
　　你的目标是使得第 1 个路由器和第 2 个路由器之间的网络连接经过尽量少的中转路由器。请问在最优方案下中转路由器的最少个数是多少?

**输入格式**　　

​	第一行包含四个正整数 n,m,k,r。$(2 ≤ n ≤ 100,1 ≤ k ≤ m ≤ 100, 1 ≤ r ≤ 108)$。
　　接下来 n 行,每行包含两个整数 xi 和 yi,表示一个已经放置好的无线 路由器在 (xi, yi) 点处。输入数据保证第 1 和第 2 个路由器在仅有这 n 个路由器的情况下已经可以互相连接(经过一系列的中转路由器)。
　　接下来 m 行,每行包含两个整数 xi 和 yi,表示 (xi, yi) 点处可以增设 一个路由器。
　　输入中所有的坐标的绝对值不超过 108,保证输入中的坐标各不相同。

**输出格式**　　

​	输出只有一个数,即在指定的位置中增设 k 个路由器后,从第 1 个路 由器到第 2 个路由器最少经过的中转路由器的个数。

**样例输入**

​	5 3 1 3
​	0 0
​	5 5
​	0 3
​	0 5
​	3 5
​	3 3
​	4 4
​	3 0

**样例输出**

​	2

## 2、 题解

**二维最短路**
​	两个路由器可以通讯则二者连一条无向边。这是一个求最优问题，通常用BFS（广度优先搜索）来实现。据称，这里用SPFA算法来实现。
​	d\[i]\[j]表示从起点开始经过增设的j个路由器到达i的最短路径，vis\[i]\[j]表示是否可以从起点经过增设的j个路由器到达i。
然后就和普通的最短路一样了。

​	数组visited[]用于标记访问过的坐标。函数bfs()的参数是为程序通用性而设置的，就本问题而言，可以使用常量。

​	最后d\[1]\[0]~d\[1]\[k]的最小值就是答案f\[i]\[k]。

**SPFA**

​	SPFA是一种单源最短路径算法，所以以下所说的“某点的最短路径长度”，指的是“某点到源点的最短路径长度”。

我们记源点为S，由源点到达点i的“当前最短路径”为D[i]，开始时将所有D[i]初始化为无穷大，D[S]则初始化为0。算法所要做的，就是在运行过程中，不断尝试减小D[]数组的元素，最终将其中每一个元素减小到实际的最短路径。

​	过程中，我们要维护一个队列，开始时将源点置于队首，然后反复进行这样的操作，直到队列为空：

​	(1)从队首取出一个结点u，扫描所有由u结点可以一步到达的结点，具体的扫描过程，随存储方式的不同而不同；

​	(2)一旦发现有这样一个结点，记为v，满足D[v] > D[u] + w(u, v)，则将D[v]的值减小，减小到和D[u] + w(u, v)相等。其中，w(u, v)为图中的边u-v的长度，由于u-v必相邻，所以这个长度一定已知（不然我们得到的也不叫一个完整的图）；这种操作叫做松弛。

> ​	松弛操作的原理是著名的定理：“三角形两边之和大于第三边”，在信息学中我们叫它三角不等式。所谓对i,j进行松弛，就是判定是否d[j]>d[i]+w[i,j]，如果该式成立则将d[j]减小到d[i]+w[i,j]，否则不动。

​	(3)上一步中，我们认为我们“改进了”结点v的最短路径，结点v的当前路径长度D[v]相比于以前减小了一些，于是，与v相连的一些结点的路径长度可能会相应地减小。注意，是可能，而不是一定。但即使如此，我们仍然要将v加入到队列中等待处理，以保证这些结点的路径值在算法结束时被降至最优。当然，如果连接至v的边较多，算法运行中，结点v的路径长度可能会多次被改进，如果我们因此而将v加入队列多次，后续的工作无疑是冗余的。这样，就需要我们维护一个bool数组Inqueue[]，来记录每一个结点是否已经在队列中。我们仅将尚未加入队列的点加入队列。

## 3、 程序

```c++

#include <iostream> 
#include <algorithm>  
#include <cstdio>  
#include <cstring>   
#include <queue>  
#define N 207  
#define oo 0x3f3f3f3f  
typedef long long LL;  
using namespace std;  
  
struct node{  
    int x,y;  
}po[205];  
  
int n,m,k,r,ans,d[205][205];  
bool vis[205][205],con[205][205];  
  
void spfa(){  
    int i;
    queue<node> q;  
    memset (d,oo,sizeof(d));
    d[0][0]=0;
    vis[0][0]=1;
    node u,tmp;
    u.x=u.y=0;
    q.push(u);
    while(!q.empty()){
        u=q.front();
        q.pop();
        vis[u.x][u.y]=0;
        for(i=0;i<n+m;i++)
            if(con[u.x][i]){
                tmp.x=i;  
                tmp.y=u.y;  
                if(i>=n) tmp.y++;  
                if(tmp.y<=k&&d[tmp.x][tmp.y]>d[u.x][u.y]+1){  
                    d[tmp.x][tmp.y]=d[u.x][u.y]+1;  
                    if(!vis[tmp.x][tmp.y]){  
                        vis[tmp.x][tmp.y]=1;  
                        q.push(tmp);  
                    }  
                }  
            }  
    }  
    ans=oo;  
    for(i=0;i<=k;i++) 
        if (d[1][i]<ans)
            ans=d[1][i];  
    cout<<ans-1<<endl;  
}  

void init (){ 
    int i,j;  
    cin>>n>>m>>k>>r;  
    for (i=0;i<n+m;i++) 
        scanf("%d%d",&po[i].x,&po[i].y);  
    for (i=0;i<n+m;i++)  
        for (j=i+1;j<n+m;j++)  
            if(LL(po[i].x-po[j].x)*(po[i].x-po[j].x)+LL(po[i].y-po[j].y)*(po[i].y-po[j].y)<=LL(r)*r) 
                con[i][j]=con[j][i]=1;  
}
  
int main(){ 
    init ();
    spfa();  
    return 0;  
}  
```



---

# CCF-CSP 201409-4	最优配餐

## 1、 题目

**问题描述**

　　栋栋最近开了一家餐饮连锁店，提供外卖服务。随着连锁店越来越多，怎么合理的给客户送餐成为了一个急需解决的问题。
　　栋栋的连锁店所在的区域可以看成是一个n×n的方格图（如下图所示），方格的格点上的位置上可能包含栋栋的分店（绿色标注）或者客户（蓝色标注），有一些格点是不能经过的（红色标注）。
　　方格图中的线表示可以行走的道路，相邻两个格点的距离为1。栋栋要送餐必须走可以行走的道路，而且不能经过红色标注的点。
![img](http://118.190.20.162/RequireFile.do?fid=383qHJjQ)
　　送餐的主要成本体现在路上所花的时间，每一份餐每走一个单位的距离需要花费1块钱。每个客户的需求都可以由栋栋的任意分店配送，每个分店没有配送总量的限制。
　　现在你得到了栋栋的客户的需求，请问在最优的送餐方式下，送这些餐需要花费多大的成本。

**输入格式**

　　输入的第一行包含四个整数n, m, k, d，分别表示方格图的大小、栋栋的分店数量、客户的数量，以及不能经过的点的数量。
　　接下来m行，每行两个整数xi, yi，表示栋栋的一个分店在方格图中的横坐标和纵坐标。
　　接下来k行，每行三个整数xi, yi, ci，分别表示每个客户在方格图中的横坐标、纵坐标和订餐的量。（注意，可能有多个客户在方格图中的同一个位置）
　　接下来d行，每行两个整数，分别表示每个不能经过的点的横坐标和纵坐标。

**输出格式**

　　输出一个整数，表示最优送餐方式下所需要花费的成本。

　　输出一个整数，表示最优送餐方式下所需要花费的成本。

**样例输入**

​	10 2 3 3
​	1 1
​	8 8
​	1 5 1
​	2 3 3
​	6 7 2
​	1 2
​	2 2
​	6 8

**样例输出**

​	29

**评测用例规模与约定**

　　前30%的评测用例满足：$1<=n <=20$。
　　前60%的评测用例满足：$1<=n<=100$。
　　所有评测用例都满足：$1<=n<=1000，1<=m, k, d<=n^2$。可能有多个客户在同一个格点上。每个客户的订餐量不超过1000，每个客户所需要的餐都能被送到。

## 2、 题解

**BFS**

​	bfs求所有源点和所有目标点之间的最短距离。

- 注意最后结果超int范围要用long long定义

## 3、 程序

```c++
#include <iostream>
#include <stdio.h>
#include <algorithm>
#include <string.h>
#include <queue>
using namespace std;

int n,m,k,d,addx[4]={-1,1,0,0},addy[4]={0,0,-1,1},order[1007][1007];
long long ans;
bool f[1007][1007];

struct node{
    int x,y,dis;
    node ();
    node (int tx,int ty,int z){
        x=tx,y=ty;
        dis=z;
    }
};

queue<node> q;

void init(){
    cin>>n>>m>>k>>d;
    int i,x,y,z;
    for (i=0;i<m;i++){
        cin>>x>>y;
        q.push(node(x,y,0));
    }
    for (i=0;i<k;i++){
        cin>>x>>y>>z;
        order[x][y]+=z;
    }
    for (i=0;i<d;i++){
        cin>>x>>y;
        f[x][y]=1;
    }
}

void bfs(){
	int xx,yy,i;
    while(!q.empty()){
        node u=q.front();
        q.pop();
        for(i=0;i<4;i++){
            xx=u.x+addx[i],yy=u.y+addy[i];
            if(xx>=1&&xx<=n&&yy>=1&&yy<=n&&!f[xx][yy]){
                ans+=(long long)order[xx][yy]*(u.dis+1);
                f[xx][yy]=1;
                q.push(node(xx,yy,u.dis+1));
            }
        }
    }
    cout<<ans<<endl;
}

int main(){
    init();
    bfs();
    return 0;
}
```
---

# CCF-CSP 201409-5 拼图

## 1、题目

**问题描述**

　　给出一个n×m的方格图，现在要用如下L型的积木拼到这个图中，使得方格图正好被拼满，请问总共有多少种拼法。其中，方格图的每一个方格正好能放积木中的一块。积木可以任意旋转。
![img](http://118.190.20.162/RequireFile.do?fid=DNHB9nN9)

**输入格式**

　　输入的第一行包含两个整数n, m，表示方格图的大小。

输出格式

　　输出一行，表示可以放的方案数，由于方案数可能很多，所以请输出方案数除以1,000,000,007的余数。

**样例输入**

6 2

**样例输出**

4

**样例说明**

　　四种拼法如下图所示：

![img](http://118.190.20.162/RequireFile.do?fid=2yqET7m8)

**评测用例规模与约定**

　　在评测时将使用10个评测用例对你的程序进行评测。
　　评测用例1和2满足：1<=n<=30，m=2。
　　评测用例3和4满足：1<=n, m<=6。
　　评测用例5满足：1<=n<=100，1<=m<=6。
　　评测用例6和7满足：1<=n<=1000，1<=m<=6。
　　评测用例8、9和10满足：1<=n<=10^15，1<=m<=7。

## 2、题解

​	通过数据范围推测解决题目的算法是一个常用技巧，此题就是一个非常典型的例子。m的范围为1<=M<=7，让我们想到**状态压缩动态规划（DP）**。但是N范围很大，达到$10^{15}$，只能想到用**矩阵快速幂优化DP**了。

​	想好用什么算法算是成功了一大半，下面开始抽丝剥茧，仔细推导动态转移方程和矩阵构造。

### 1. 状态压缩动态规划

​	我们用0代表此处留空，1代表此处被填满。01序列压缩成一个int型十进制数来表示一行的填放情况。（例如：状态为9，则代表1001，即第一、四列填满，第二、三列空）。

**递推矩阵**如下:

$$ \left[\begin{matrix}f[i][s_0]\\f[i][s_1]\\.\\.\\f[i][s_t]\end{matrix}\right]=\left[\begin{matrix}D_{0,0} & D_{0,1} &…& D_{0,t} \\D_{1,0} & D_{1,1} &…& D_{1,t} \\.&.&.&.\\.&.&.&.\\D_{t,0}&D_{t,1}&…&D_{t,t}\end{matrix} \right] \left[\begin{matrix}f[i-1][s_0]\\f[i-1][s_1]\\.\\.\\f[i-1][s_t]\end{matrix}\right]$$

初始化：

- s低m位都是1：f\[o]\[s]=1;
- s低m位存在0：f\[0][s]=0;

变量意义：

> - t = $2^m$
> - $f[i][s]$代表将前i-1行填满，且第i行放置了状态s时的总方案数。
> - $D_{s1,s2}$代表上一行原本放置了状态s2的前提下，当前行放置骨牌把上一行填满并使得当前行状态为s1的方案数。

​	有了转移方程，现在的问题是如何得到D矩阵呢？

​	枚举上一行状态s2，对每个s2进行dfs：依次扫描s2的每一位，如果有空位置，则尝试拜访某个方向的骨牌（共4种方向）。当把s2所有位置拜满以后，s1便产生了，那么让$D_{s1,s2}$增加1 （初始为0）。

​	最终的最终，我们终于得到：

$$ \left[\begin{matrix}f[n][s_0]\\f[n][s_1]\\.\\.\\f[n][s_t]\end{matrix}\right]=\left[\begin{matrix}D_{0,0} & D_{0,1} &…& D_{0,t} \\D_{1,0} & D_{1,1} &…& D_{1,t} \\.&.&.&.\\.&.&.&.\\D_{t,0}&D_{t,1}&…&D_{t,t}\end{matrix} \right]^n \left[\begin{matrix}f[0][s_0]\\f[0][s_1]\\.\\.\\f[0][s_t]\end{matrix}\right]$$

​	$f[n][s_t]$就是结果。

​	但是n很大，暴力地做n边矩阵乘法无疑会超时，这时候就要用到一个叫**快速幂**的神奇算法。

### 2. 快速幂

​	顾名思义，快速幂就是快速算底数的n次幂。其时间复杂度为 O(log₂N)， 与朴素的O(N)相比效率有了极大的提高。

​	以下以求a的b次方来介绍，把b转换成二进制数。该二进制数第i位的权为：$2^{i-1}$。

> 例如$a^{11}=a^{2^0+2^1+2^3}$
>
> 11的二进制是1011
>
> $11 = 2³×1 + 2²×0 + 2¹×1 + 2º×1$
>
> 因此，我们将$a¹¹$转化为算$a^{2^0+2^1+2^3}$.

**实现：**

递归版：

```c++
ll pow(ll a,ll i){
  if (i==0) return 1;
  int temp=pow(a,i>>1);
  temp=temp*temp%MOD;
  if (i&1) temp=(ll)temp*a%MOD;
  return temp%MOD;
}
```

非递归版：

```c++
ll f(ll a,ll b,ll n){
  int t,y;
  t=1; y=a;
  while (b!=0){
    if (b&1==1) t=t*y%n;
    y=y*y%n; b=b>>1;
  }
  return t;
}
```

​	用矩阵快速幂优化状态压缩DP，得到的$f[n][s_t]$就是最终的结果。

## 3、程序

```c++
#include <iostream>  
#include <cstdio>  
#include <cstring>  
using namespace std;  
  
typedef long long LL;  
const int mod=1000000007;  
const int maxn=207;  
int add[5]={0,1,1,2,2},d[maxn][maxn],m,t; //总状态数 1<<m    
/* 
各个方向的骨牌及其对应编号 
 1  * 
    ** 
 
 2  * 
   ** 
 
 3  ** 
    * 
 
 4  ** 
     * 
*/ 
LL n;  
bool f[2][10];  
  
void num_matrix(int num,bool matrix[]){//数字转矩阵  
    for(int i=0;i<m;++i,num>>=1)  
        matrix[m-i-1]=num&1;  
}  
  
int matrix_num(bool matrix[]){//矩阵转数字   
    int num=0;  
    for(int i=0;i<m;(num<<=1)|=matrix[i++]);  
    return num;  
}  
  
bool ok(int tmp,int type){//判断在tmp位置是否可以放置type方向的骨牌  
    if(type==1) return f[0][tmp]==0&&f[1][tmp]==0&&tmp+1<m&&f[1][tmp+1]==0;  
    if(type==2) return f[0][tmp]==0&&f[1][tmp]==0&&tmp-1>=0&&f[1][tmp-1]==0;  
    if(type==3) return f[0][tmp]==0&&f[1][tmp]==0&&tmp+1<m&&f[0][tmp+1]==0;  
    if(type==4) return f[0][tmp]==0&&tmp+1<m&&f[0][tmp+1]==0&&f[1][tmp+1]==0;  
}  
  
void put(int tmp,int type,int cont){//在tmp位置放置type方向的骨牌  
    if(type==1) f[1][tmp]=f[1][tmp+1]=cont;  
    if(type==2) f[1][tmp]=f[1][tmp-1]=cont;  
    if(type==3) f[1][tmp]=cont;  
    if(type==4) f[1][tmp+1]=cont;  
}  
  
void dfs(int tmp){  
    if(tmp>=m){
        ++d[matrix_num(f[1])][matrix_num(f[0])];  
        return;  
    }  
    if(f[0][tmp]==1) dfs(tmp+1);//如果当前位置已填，继续尝试下一列  
    for(int i=1;i<=4;++i)  
        if(ok(tmp,i)){//如果当前位置可以放置i方向骨牌    
            put(tmp,i,1);//尝试放置i方向骨牌  
            dfs(tmp+add[i]);//继续尝试后面列  
            put(tmp,i,0);//撤销放置i方向骨牌  
        }  
}  
  
void getd(){//计算矩阵D   
    t=1<<m;  
    memset(d,0,sizeof(d));  
    for(int i=0;i<t;++i){  
        num_matrix(i,f[0]);  
        dfs(0);  
    }
  
}  
  
void mult(int a[maxn][maxn],int b[maxn][maxn]){//矩阵乘法a*=b  
    static int c[maxn][maxn];  
    memset(c,0,sizeof(c));  
    for(int i=0;i<t;++i)  
        for(int j=0;j<t;++j)  
            for(int k=0;k<t;++k)  
                c[i][j]=(c[i][j]+((LL)a[i][k]*(LL)b[k][j]) % mod) % mod;  
    memcpy(a,c,sizeof(c));  
}  
  
void cal(int a[maxn][maxn],LL p){//矩阵快速幂a^p  
    int ans[maxn][maxn];  
    memset(ans,0,sizeof(ans));  
    for(int i=0;i<t;++i) ans[i][i]=1;    //ans=1  
    for(;p;p>>=1,mult(a,a))  
        if(p&1)  
            mult(ans,a);//ans*=a;  
       //a*=a;  
    memcpy(a,ans,sizeof(ans)); //return ans  
}  
  
int main(){  
    cin>>n>>m;  
    getd();  
    cal(d,n);  
    cout<<d[t-1][t-1]<<endl;  
    return 0;  
}  
  
```



#  CCF-CSP 201412-4 最优灌溉

## 1、 题目

**问题描述**

　　雷雷承包了很多片麦田，为了灌溉这些麦田，雷雷在第一个麦田挖了一口很深的水井，所有的麦田都从这口井来引水灌溉。
　　为了灌溉，雷雷需要建立一些水渠，以连接水井和麦田，雷雷也可以利用部分麦田作为“中转站”，利用水渠连接不同的麦田，这样只要一片麦田能被灌溉，则与其连接的麦田也能被灌溉。
　　现在雷雷知道哪些麦田之间可以建设水渠和建设每个水渠所需要的费用（注意不是所有麦田之间都可以建立水渠）。请问灌溉所有麦田最少需要多少费用来修建水渠。

**输入格式**

　　输入的第一行包含两个正整数n, m，分别表示麦田的片数和雷雷可以建立的水渠的数量。麦田使用1, 2, 3, ……依次标号。
　　接下来m行，每行包含三个整数ai, bi, ci，表示第ai片麦田与第bi片麦田之间可以建立一条水渠，所需要的费用为ci。

**输出格式**

　　输出一行，包含一个整数，表示灌溉所有麦田所需要的最小费用。

**样例输入**

​	4 4
​	1 2 1
​	2 3 4
​	2 4 2
​	3 4 3

**样例输出**

​	6

**样例说明**

　　建立以下三条水渠：麦田1与麦田2、麦田2与麦田4、麦田4与麦田3。

**评测用例规模与约定**

　　前20%的评测用例满足：n≤5。
　　前40%的评测用例满足：n≤20。
　　前60%的评测用例满足：n≤100。
　　所有评测用例都满足：1≤n≤1000，1≤m≤100,000，1≤ci≤10,000。

## 2、 题解

​    本题属于图论题目，将每一个麦田当成一个点v，每两个可以建立水渠的麦田当做一个边（v，u）。这样题目就可以简化成：给出图的顶点数n和所有边的数目m，然后给出每条边的起点v和终点u以及权值cost。然后求解链接所有顶点的一个连通子图，求出边权和最小的图，输入最后的权值和。

     可以看出这个最后的连通子图就是原图的最小生成树。

**最小生成树**

- 定义：在一给定的无向图G = (V, E) 中，(u, v) 代表连接顶点 u 与顶点 v 的边，而 w(u, v) 代表此边的权重，若存在 T 为 E 的子集且为无循环图，使得的 w(T) 最小，则此 T 为 G 的最小生成树。

- kruskal算法

  1).记Graph中有v个顶点，e个边

  2).新建图Graphnew，Graphnew中拥有原图中相同的e个顶点，但没有边

  3).将原图Graph中所有e个边按权值从小到大排序

  4).循环：从权值最小的边开始遍历每条边 直至图Graph中所有的节点都在同一个连通分量中

                  if 这条边连接的两个节点于图Graphnew中不在同一个连通分量中

                                           添加这条边到图Graphnew中

- Prim算法

  1).输入：一个加权连通图，其中顶点集合为V，边集合为E；

  2).初始化：Vnew = {x}，其中x为集合V中的任一节点（起始点），Enew = {},为空；

  3).重复下列操作，直到Vnew = V：

  a.在集合E中选取权值最小的边<u, v>，其中u为集合Vnew中的元素，而v不在Vnew集合当中，并且v∈V（如果存在有多条满足前述条件即具有相同权值的边，则可任意选取其中之一）；

  b.将v加入集合Vnew中，将<u, v>边加入集合Enew中；

  4).输出：使用集合Vnew和Enew来描述所得到的最小生成树。

## 3、 程序

```c++
#include <iostream>  
#include <algorithm>  
using namespace std; 
  
int fa[1007],n,m,ans;

struct Node{ 
    int from,to,price; 
}c[100007]; 
  
int getfa(int x){  
    if (x==fa[x])return x;
    else return fa[x]=getfa(fa[x]);
}  
  
bool cmp(Node x,Node y){
    return x.price<y.price; 
}  
  
void init (){
	int i;
    cin>>n>>m; 
    for (i=1;i<=n;i++)fa[i]=i;
    for (i=1;i<=m;i++)  
        cin>>c[i].from>>c[i].to>>c[i].price;
    sort(c+1,c+1+m,cmp);
}

int main(){
    init ();  
    int i; 
    for(i=1;i<=m;i++){  
        if(getfa(c[i].from)!=getfa(c[i].to)){
            ans+=c[i].price; 
            fa[getfa(c[i].to)]=getfa(c[i].from); 
        }  
    }  
    cout<<ans<<endl; 
    return 0; 
}   
```



---

# CCF-CSP 201503-4 网络延时

## 1、 题目

**问题描述**

　　给定一个公司的网络，由n台交换机和m台终端电脑组成，交换机与交换机、交换机与电脑之间使用网络连接。交换机按层级设置，编号为1的交换机为根交换机，层级为1。其他的交换机都连接到一台比自己上一层的交换机上，其层级为对应交换机的层级加1。所有的终端电脑都直接连接到交换机上。
　　当信息在电脑、交换机之间传递时，每一步只能通过自己传递到自己所连接的另一台电脑或交换机。请问，电脑与电脑之间传递消息、或者电脑与交换机之间传递消息、或者交换机与交换机之间传递消息最多需要多少步。

输入格式

　　输入的第一行包含两个整数n, m，分别表示交换机的台数和终端电脑的台数。
　　第二行包含n - 1个整数，分别表示第2、3、……、n台交换机所连接的比自己上一层的交换机的编号。第i台交换机所连接的上一层的交换机编号一定比自己的编号小。
　　第三行包含m个整数，分别表示第1、2、……、m台终端电脑所连接的交换机的编号。

**输出格式**

　　输出一个整数，表示消息传递最多需要的步数。

**样例输入**

4 2
1 1 3
2 1

**样例输出**

4

**样例说明**

　　样例的网络连接模式如下，其中圆圈表示交换机，方框表示电脑：
![img](http://118.190.20.162/RequireFile.do?fid=F9GfBRHL)
　　其中电脑1与交换机4之间的消息传递花费的时间最长，为4个单位时间。

**样例输入**

4 4
1 2 2
3 4 4 4

**样例输出**

4

**样例说明**

　　样例的网络连接模式如下：
![img](http://118.190.20.162/RequireFile.do?fid=LYDFDEbt)
　　其中电脑1与电脑4之间的消息传递花费的时间最长，为4个单位时间。

**评测用例规模与约定**

　　前30%的评测用例满足：n ≤ 5, m ≤ 5。
　　前50%的评测用例满足：n ≤ 20, m ≤ 20。
　　前70%的评测用例满足：n ≤ 100, m ≤ 100。
　　所有评测用例都满足：1 ≤ n ≤ 10000，1 ≤ m ≤ 10000。

## 2、 题解

**树的直径：**

​	从任意一点u出发搜到的最远的点一定是s、t中的一点，然后在从这个最远点开始搜，就可以搜到另一个最长路的端点，即用两遍广搜就可以找出树的最长路

反证法：

​	假设 s-t这条路径为树的直径，或者称为树上的最长路

1. 设u为s-t路径上的一点，结论显然成立，否则设搜到的最远点为T则 dis(u,T) >dis(u,s) 且 dis(u,T)>dis(u,t) 则最长路不是s-t了，与假设矛盾。
2. 设u不为s-t路径上的点

  首先明确，假如u走到了s-t路径上的一点，那么接下来的路径肯定都在s-t上了，而且终点为s或t，在1中已经证明过了
  所以现在又有两种情况了：
  1：u走到了s-t路径上的某点，假设为X，最后肯定走到某个端点，假设是t ，则路径总长度为dis(u,X)+dis(X,t)
  2：u走到最远点的路径u-T与s-t无交点，则dis(u-T) >dis(u,X)+dis(X,t);显然，如果这个式子成立，  则dis(u,T)+dis(s,X)+dis(u,X)>dis(s,X)+dis(X,t)=dis(s,t)最长路不是s-t矛盾

![](3.png)

## 3、 程序

```c++
#include <iostream>   
#include <string.h>  
#include <vector>  
using namespace std; 

int n,m,dis[100010],maxi,maxd;  
bool vis[100010];
vector<int>node[100010];
  
void dfs(int u){  
  int i,v;  
  if(maxd<dis[u]){  
    maxd=dis[u];  
    maxi=u;  
  }  
  for(i=0;i<node[u].size();i++){  
       v=node[u][i];  
       if (!vis[v]){  
         vis[v]=1;  
      	 dis[v]=dis[u]+1;  
         dfs(v);  
       }  
  }  
}  

void init (){
	int i,x;
    cin>>n>>m;  
    for (i=2;i<=n;i++){  
    	cin>>x;  
      	node[i].push_back(x);  
      	node[x].push_back(i);  
    }  
    for(i=1;i<=m;i++){  
        cin>>x;  
      	node[n+i].push_back(x);  
      	node[x].push_back(n+i);  
    }  
	
}
int main(){  
    int i,x,num1;  
    init ();
    memset(vis,0,sizeof (vis));  
    memset(dis,0,sizeof (dis));  
    maxd=-1;  
    vis[1]=1;  
    dfs(1);  
    
    memset(vis,0,sizeof (vis));  
    memset(dis,0,sizeof (dis));   
    maxd=-1;  
    vis[maxi]=1;  
    dfs(maxi);  
    cout<<maxd<<endl;  
    return 0;  
}  
```
---

# CCF-CSP 201503-5 最小花费

## 1、题目

**问题描述**

　　然而，小R不希望在旅行中为了购买较低价的粮食而绕远路，因此他总会选择最近的路走。现在，请你计算小R规划的每条旅行路线的最小花费是多少。

**输入格式**

　　第一行包含2个整数n和m。
　　第二行包含n个整数。第i个整数wi表示城市i的食物价格。
　　接下来n-1行，每行包括3个整数u, v, e，表示城市u和城市v之间有一条长为e的双向道路。
　　接下来m行，每行包含2个整数si和ti，分别表示一条旅行路线的起点和终点。

**输出格式**

　　输出m行，分别代表每一条旅行方案的最小花费。

**样例输入**

6 4
1 7 3 2 5 6
1 2 4
1 3 5
2 4 1
3 5 2
3 6 1
2 5
4 6
6 4
5 6

**样例输出**

35
16
26
13

**样例说明**

　　对于第一条路线，小R会经过2->1->3->5。其中在城市2处以7的价格购买4单位粮食，到城市1时全部吃完，并用1的价格购买7单位粮食，然后到达终点。

**评测用例规模与约定**

　　前10%的评测用例满足：n, m ≤ 20, wi ≤ 20；
　　前30%的评测用例满足：n, m ≤ 200；
　　另有40%的评测用例满足：一个城市至多与其它两个城市相连。
　　所有评测用例都满足：1 ≤ n, m ≤ 105，1 ≤ wi ≤ 106，1 ≤ e ≤ 10000。

## 2、题解

​	首先我们由题意很容易分析出，一条路径的选择方案，一定是从一个点（a）走到下一个比它便宜的点，这之间的食物都在a点购买。

​	而这个信息不具有可加性，却具有可减性。

​	下面介绍一种基于点分治的做法。

> **分治算法**
>
> - 定义：
>
>   ​	分治算法的基本思想是将一个规模为N的问题分解为K个规模较小的子问题，这些子问题相互独立且与原问题性质相同。求出子问题的解，就可得到原问题的解。即一种分目标完成程序算法，简单问题可用二分法完成。
>
> - 解决步骤：
>
>   （1）分解，将要解决的问题划分成若干规模较小的同类问题；
>
>   （2）求解，当子问题划分得足够小时，用较简单的方法解决；
>
>   （3）合并，按原问题的要求，将子问题的解逐层合并构成原问题的解。
>
>   ![](4.png)



​	假设当前分治中心为T，对于一个询问u->v,可以被拆成u->T,T->v，对于u->T的费用，我们可以在倍增数组上二分出u上面第一个比它便宜的位置，用这个位置的信息可以直接得出u的信息。

​	现在考虑T->v的费用，对于每一个询问，都附加了一个状态，表示之前便宜的费用c，我们需要在T->v的路径上找到第一个比它便宜的点设为x（这个可以通过dfs时维护一个前缀最小值数据来二分求得），这一段用的费用是dis(T, x) * c，剩下的部分就是从x向下走走到v的费用，我们可以通过dfs求出每个点到T的费用，之前已经提到过，维护的信息具有可减性，就可以$O(1)$的时间算出从一个点往下走的走到某个点的费用。

​	空间复杂度：$O(n\log n)$

## 3、程序

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
#define FI first
#define SE second
#define for_edge(u,it) for(vector<pair<int,int>>::iterator it=G[u].begin();it!=G[u].end();++it)

const int N=100000+10;

vector<pair<int,int>> G[N];
vector<int> Q[N],q1[N],q2[N];
LL dis[N],disv[N];
int cost[N],sz[N],maxsz[N],top[N],root;
pair<int,int> q_info[N];
pair<LL,int> ans[N];
bool centre[N];

#define v it->FI
void get_size(int u,int fa) {
    maxsz[u]=0,sz[u]=1;
    for_edge(u,it) if(v!=fa&&!centre[v]) {
        get_size(v,u);
        sz[u]+=sz[v];
        maxsz[u]=max(maxsz[u],sz[v]);
    }
}

void get_root(int u,int fa,int r) {
    maxsz[u]=max(maxsz[u],sz[r]-sz[u]);
    if(maxsz[u]<maxsz[root]) root=u;
    for_edge(u,it) if(v!=fa&&!centre[v]) {
        get_root(v,u,r);
    }
}

int anc[17][N],val[17][N];

int tot_time;

void get_top(int u,int fa,int pre) {
    anc[0][u]=fa,val[0][u]=cost[u];
    int tmp=clock();
    for(int i=1;i<17;i++) {
        val[i][u]=min(val[i-1][u],val[i-1][anc[i-1][u]]);
        anc[i][u]=anc[i-1][anc[i-1][u]];
    }
    tot_time+=clock()-tmp;
        
    top[u]=pre;
    for_edge(u,it) if(v!=fa&&!centre[v]) {
        dis[v]=dis[u]+it->SE,get_top(v,u,pre);
    }
}

int pre[N];

int find(int u) {
    int cost_u=cost[u];
    for(int i=16;i >= 0;i--) {
        if(val[i][u] >= cost_u) u=anc[i][u];
    }
    return u;
}

void calc_1(int u,int fa) {
    int anc=find(u);
    if(anc) pre[u]=pre[anc];
    else anc=root,pre[u]=u;
    disv[u]=(dis[u]-dis[anc])*cost[u]+disv[anc];
    for(unsigned i=0;i<q1[u].size();i++) {
        ans[q1[u][i]]=make_pair(disv[u],cost[pre[u]]);
    }
    for_edge(u,it) if(v!=fa&&!centre[v]) {
        calc_1(v,u);
    }
}

void calc_2(int u,int fa,int fee) {
    static int val[N],id[N],tot;
    disv[u]=(dis[u]-dis[fa])*fee+disv[fa];
    id[tot]=u,val[tot]=cost[u];
    if(tot++) val[tot-1]=min(val[tot-2],cost[u]);
    id[tot]=u;
    for(unsigned i=0;i<q2[u].size();i++) {
        int c=q2[u][i];
        int anc=id[lower_bound(val,val+tot,ans[c].SE,greater<int>())-val];
        ans[c].FI+=ans[c].SE*(dis[anc]-dis[root])+disv[u]-disv[anc];
    }
    for_edge(u,it) if(v!=fa&&!centre[v]) {
        calc_2(v,u,min(fee,cost[u]));
    }
    --tot;
}

void work(int u) {
    if(!Q[u].size()) return;
    get_size(u,0);
    root=u,get_root(u,0,u);
    vector<int> vec_q;
    vec_q.swap(Q[u]);
    centre[u=root]=1,top[u]=u,dis[u]=0;
    for(int i=0;i<17;i++) anc[i][u]=val[i][u]=0;
    val[0][u]=cost[u];
    for_edge(u,it) if(!centre[v]) {
        dis[v]=it->SE,get_top(v,u,v);
    }
    for(unsigned i=0;i<vec_q.size();i++) {
        int c=vec_q[i],x=q_info[c].FI,y=q_info[c].SE;
        if(top[x]==top[y]) Q[top[x]].push_back(c);
        else q1[x].push_back(c),q2[y].push_back(c);
    }
    disv[u]=0,pre[u]=u;
    calc_1(u,0);
    disv[u]=0;
    calc_2(root,0,cost[u]);
    for(unsigned i=0;i<vec_q.size();i++) {
        int c=vec_q[i],x=q_info[c].FI,y=q_info[c].SE;
        q1[x].clear(),q2[y].clear();
    }
    for_edge(u,it) if(!centre[v]) {
        work(v);
    }
}
#undef v
int main() {
    int n,m,u,v,w;
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;i++) {
        scanf("%d",cost+i);
    }
    for(int i=1;i<n;i++) {
        scanf("%d%d%d",&u,&v,&w);
        G[u].push_back(pair<int,int>(v,w));
        G[v].push_back(pair<int,int>(u,w));
    }
    for(int i=0;i<m;i++) {
        pair<int,int> &cur=q_info[i];
        scanf("%d%d",&cur.FI,&cur.SE);
        if(cur.FI!=cur.SE) Q[1].push_back(i);
    }
    work(1);
    for(int i=0;i<m;i++) {
        printf("%I64d\n",ans[i].FI);
    }
    return 0;
}
```

---

# CCF-CSP 201509-4 高速公路 

## 1、题目

**问题描述**

　　某国有n个城市，为了使得城市间的交通更便利，该国国王打算在城市之间修一些高速公路，由于经费限制，国王打算第一阶段先在部分城市之间修一些单向的高速公路。
　　现在，大臣们帮国王拟了一个修高速公路的计划。看了计划后，国王发现，有些城市之间可以通过高速公路直接（不经过其他城市）或间接（经过一个或多个其他城市）到达，而有的却不能。如果城市A可以通过高速公路到达城市B，而且城市B也可以通过高速公路到达城市A，则这两个城市被称为便利城市对。
　　国王想知道，在大臣们给他的计划中，有多少个便利城市对。

**输入格式**

　　输入的第一行包含两个整数n, m，分别表示城市和单向高速公路的数量。
　　接下来m行，每行两个整数a, b，表示城市a有一条单向的高速公路连向城市b。

**输出格式**

　　输出一行，包含一个整数，表示便利城市对的数量。

**样例输入**

5 5
1 2
2 3
3 4
4 2
3 5

**样例输出**

3

**样例说明**

![img](http://118.190.20.162/RequireFile.do?fid=4HG9GgbF)

​	城市间的连接如图所示。有3个便利城市对，它们分别是(2, 3), (2, 4), (3, 4)，请注意(2, 3)和(3, 2)看成同一个便利城市对。

**评测用例规模与约定**

　　前30%的评测用例满足1 ≤ n ≤ 100, 1 ≤ m ≤ 1000；
　　前60%的评测用例满足1 ≤ n ≤ 1000, 1 ≤ m ≤ 10000；
　　所有评测用例满足1 ≤ n ≤ 10000, 1 ≤ m ≤ 100000

## 2、题解

​	根据题意，先求强连通分量，再从各个元素大于1的强连通分量中选2个城市的组合，最后再相加，得到答案。
​	这里求强连通分量用到的是tarjan算法。

**Tarjan算法**

​	 说到以Tarjan命名的算法，我们经常提到的有3个，其中就包括本文所介绍的求强连通分量的Tarjan算法。而提出此算法的普林斯顿大学的Robert E Tarjan教授也是1986年的图灵奖获得者。

>       首先明确几个概念。
>
> 1. 强连通图。在一个强连通图中，任意两个点都通过一定路径互相连通。比如图一是一个强连通图，而图二不是。因为没有一条路使得点4到达点1、2或3。![img](http://pic002.cnblogs.com/images/2010/150814/2010110807295234.png)
> 2. 强连通分量。在一个非强连通图中极大的强连通子图就是该图的强连通分量。比如图三中子图{1,2,3,5}是一个强连通分量，子图{4}是一个强连通分量。
>
> ![img](http://pic002.cnblogs.com/images/2010/150814/2010110807314062.png)
>
>            其实，tarjan算法的基础是DFS。我们准备两个数组Low和Dfn。Low数组是一个标记数组，记录该点所在的强连通子图所在搜索子树的根节点的Dfn值（很绕嘴，往下看你就会明白），Dfn数组记录搜索到该点的时间，也就是第几个搜索这个点的。根据以下几条规则，经过搜索遍历该图（无需回溯）和对栈的操作，我们就可以得到该有向图的强连通分量。
>
> 1. 数组的初始化：当首次搜索到点p时，Dfn与Low数组的值都为到该点的时间。
> 2. 堆栈：每搜索到一个点，将它压入栈顶。
> 3. 当点p有与点p’相连时，如果此时（时间为dfn[p]时）p’不在栈中，p的low值为两点的low值中较小的一个。
> 4. 当点p有与点p’相连时，如果此时（时间为dfn[p]时）p’在栈中，p的low值为p的low值和p’的dfn值中较小的一个。
> 5. 每当搜索到一个点经过以上操作后（也就是子树已经全部遍历）的low值等于dfn值，则将它以及在它之上的元素弹出栈。这些出栈的元素组成一个强连通分量。
> 6. 继续搜索（或许会更换搜索的起点，因为整个有向图可能分为两个不连通的部分），直到所有点被遍历。
>
>       由于每个顶点只访问过一次，每条边也只访问过一次，我们就可以在O（n+m）的时间内求出有向图的强连通分量。但是，这么做的原因是什么呢？
>
> 
>
>       Tarjan算法的操作原理如下：
>
> 1. Tarjan算法基于定理：在任何深度优先搜索中，同一强连通分量内的所有顶点均在同一棵深度优先搜索树中。也就是说，强连通分量一定是有向图的某个深搜树子树。
> 2. 可以证明，当一个点既是强连通子图Ⅰ中的点，又是强连通子图Ⅱ中的点，则它是**强连通子图**Ⅰ∪Ⅱ中的点。
> 3. 这样，我们用low值记录该点所在强连通子图对应的搜索子树的根节点的Dfn值。注意，该子树中的元素在栈中一定是相邻的，且根节点在栈中一定位于所有子树元素的最下方。
> 4. 强连通分量是由若干个环组成的。所以，当有环形成时（也就是搜索的下一个点已在栈中），我们将这一条路径的low值统一，即这条路径上的点属于同一个强连通分量。
> 5. 如果遍历完整个搜索树后某个点的dfn值等于low值，则它是该搜索子树的根。这时，它以上（包括它自己）一直到栈顶的所有元素组成一个强连通分量。
>
> 文章来源:http://www.cnblogs.com/saltless

## 3、程序

```c++
#include <iostream>
#include <stdio.h>
#include <string.h>
#define min(a,b) a>b ? b:a
using namespace std;

const int maxn=10001;
const int maxm=50001;

struct EdgeTp{
	int adj;
 	int next;
}g[maxm];

int n,m,tot,top,ans;
int first[maxn],dfn[maxn],low[maxn];
int stack[maxn],link[maxn];

void init(){
	int i,x,y;
 	memset(first,0,sizeof(first));
 	scanf("%d %d",&n,&m);
 	tot=0;
 	for (i=1;i<=m;i++){//存边表
   		scanf("%d %d",&x,&y);
    	tot++;
    	g[tot].adj=y;
    	g[tot].next=first[x];
    	first[x]=tot; 
   	}
 	tot=0;
}

void dfs(int x){ //求强连通分量
	int i,t,y,tmp;
 	bool  flag;
 	dfn[x]=++tot;
 	low[x]=tot; //设置节点信息
 	stack[++top]=x;
 	link[x]=top;//入栈，记录该元素在栈中的位置
 	flag=true; 
 	t=first[x]; //依次遍历它对应的每条出边
 	while (t!=0){
   		y=g[t].adj;
    	if (dfn[y]==0){ //若该点未被访问过
      		dfs(y);
       	low[x]=min(low[x],low[y]); //更新low
      	}
    	else if (link[y]!=0)
        	low[x]=min(low[x],dfn[y]); //如果该点被访问了，则该弧为后向弧或交叉弧，若处于一个连通分量里，则更新low
    	t=g[t].next;
   	}
 	if (low[x]==dfn[x]){ //如果low=dfn，则求出强连通分量
    	//if (flag){ //若flag为true，则找到一个出度为0的强连通分量}
        tmp=top-link[x]+1; //更新ans
        ans+=tmp*(tmp-1)/2;
    	t=top;
    	top=link[x]-1; //top指针退回
    	for (i=link[x];i<=t;i++)
      		link[stack[i]]=0; //由于该连通分量中的点已经不在连通分量里了，所以进行撤销赋值
   	}
}

int main(){
	int i;
 	init();
 	for (i=1;i<=n;i++)
   		if (dfn[i]==0)
     		dfs(i);
 	printf("%d\n",ans);
 	return 0;
}
```



---

# CCF-CSP 201509-5 最佳文章

## 1、题目

**问题描述**

　　小明最近在研究一门新的语言，叫做Q语言。Q语言单词和文章都可以用且仅用只含有小写英文字母的字符串表示，任何由这些字母组成的字符串也都是一篇合法的Q语言文章。
　　在Q语言的所有单词中，小明选出了他认为最重要的n个。使用这些单词，小明可以评价一篇Q语言文章的“重要度”。
　　文章“重要度”的定义为：在该文章中，所有重要的Q语言单词出现次数的总和。其中多次出现的单词，不论是否发生包含、重叠等情况，每次出现均计算在内。
　　例如，假设n = 2，小明选出的单词是gvagv和agva。在文章gvagvagvagv中，gvagv出现了3次，agva出现了2次，因此这篇文章的重要度为3+2=5。
　　现在，小明想知道，一篇由m个字母组成的Q语言文章，重要度最高能达到多少。

**输入格式**

　　输入的第一行包含两个整数n, m，表示小明选出的单词个数和最终文章包含的字母个数。
　　接下来n行，每行包含一个仅由英文小写字母构成的字符串，表示小明选出的这n个单词。

**输出格式**

　　输出一行一个整数，表示由m个字母组成的Q语言文章中，重要度最高的文章的重要度。

**样例输入**

3 15
agva
agvagva
gvagva

样例输出

11

**样例说明**

　　15个字母组成的重要度最高的文章为gvagvagvagvagva。
　　在这篇文章中，agva出现4次，agvagva出现3次，gvagva出现4次，共计4+3+4=11次。

**评测用例规模与约定**

　　在评测时将使用10个评测用例对你的程序进行评测。
　　设s为构成n个重要单词字母的总个数，例如在样例中，s=4+7+6=17；a为构成n个重要单词字母的种类数，例如在样例中，共有3中字母'a','g','v'，因此a=3。
　　评测用例1和2满足2 ≤ n ≤ 3，1500 ≤ m ≤ 2000，s = 40；
　　评测用例3和4满足m = 20，2 ≤ a ≤ 3；
　　评测用例5、6和7满足2000 ≤ m ≤ 100000；
　　评测用例8满足n = 2；
　　所有的评测用例满足1 ≤ s ≤ 100，1 ≤ m ≤ $10^{15}$，每个单词至少包含1个字母，保证单词中仅出现英文小写字母，输入中不含多余字符，不会出现重复的单词。

## 2、题解

​	根据数据大小，很容易就想到AC自动机来优化状态，再用矩阵快速幂来优化dp，但是这题的矩阵快速幂有变形。

**AC自动机算法**

> - **AC自动机简介：** 
>
> 首先简要介绍一下AC自动机：Aho-Corasick automation，该[算法](http://lib.csdn.net/base/datastructure)在1975年产生于贝尔实验室，是著名的多模匹配算法之一。一个常见的例子就是给出n个单词，再给出一段包含m个字符的文章，让你找出有多少个单词在文章里出现过。要搞懂AC自动机，先得有字典树Trie和KMP模式匹配算法的基础知识。KMP算法是单模式串的字符匹配算法，AC自动机是多模式串的字符匹配算法。
>
> - **AC自动机的构造：**
>
> 1.构造一棵Trie，作为AC自动机的搜索[数据结构](http://lib.csdn.net/base/datastructure)。
>
> 2.构造fail指针，使当前字符失配时跳转到具有最长公共前后缀的字符继续匹配。如同 KMP算法一样， AC自动机在匹配时如果当前字符匹配失败，那么利用fail指针进行跳转。由此可知如果跳转，跳转后的串的前缀，必为跳转前的模式串的后缀并且跳转的新位置的深度（匹配字符个数）一定小于跳之前的节点。所以我们可以利用 bfs在 Trie上面进行 fail指针的求解。
>
> 3.扫描主串进行匹配。
>
> - **AC自动机详讲：**
>
> 我们给出5个单词，say，she，shr，he，her。给定字符串为yasherhs。问多少个单词在字符串中出现过。
>
> **一、Trie**
>
> 首先我们需要建立一棵Trie。但是这棵Trie不是普通的Trie，而是带有一些特殊的性质。
>
> 首先会有3个重要的指针，分别为p, p->fail, temp。
>
> 1.指针p，指向当前匹配的字符。若p指向root，表示当前匹配的字符序列为空。（root是Trie入口，没有实际含义）。
>
> 2.指针p->fail，p的失败指针，指向与字符p相同的结点，若没有，则指向root。
>
> 3.指针temp，[测试](http://lib.csdn.net/base/softwaretest)指针（自己命名的，容易理解！~），在建立fail指针时有寻找与p字符匹配的结点的作用，在扫描时作用最大，也最不好理解。
>
> 对于Trie树中的一个节点，对应一个序列s[1...m]。此时，p指向字符s[m]。若在下一个字符处失配，即p->next[s[m+1]] == NULL，则由失配指针跳到另一个节点(p->fail)处，该节点对应的序列为s[i...m]。若继续失配，则序列依次跳转直到序列为空或出现匹配。在此过程中，p的值一直在变化，但是p对应节点的字符没有发生变化。在此过程中，我们观察可知，最终求得得序列s则为最长公共后缀。另外，由于这个序列是从root开始到某一节点，则说明这个序列有可能是某些序列的前缀。
>
> 再次讨论p指针转移的意义。如果p指针在某一字符s[m+1]处失配(即p->next[s[m+1]] == NULL)，则说明没有单词s[1...m+1]存在。此时，如果p的失配指针指向root，则说明当前序列的任意后缀不会是某个单词的前缀。如果p的失配指针不指向root，则说明序列s[i...m]是某一单词的前缀，于是跳转到p的失配指针，以s[i...m]为前缀继续匹配s[m+1]。
>
> 对于已经得到的序列s[1...m]，由于s[i...m]可能是某单词的后缀，s[1...j]可能是某单词的前缀，所以s[1...m]中可能会出现单词。此时，p指向已匹配的字符，不能动。于是，令temp = p，然后依次测试s[1...m], s[i...m]是否是单词。
>
> 构造的Trie为：
>
> ![](5.png)
>
> **二、构造失败指针**
>
> 用BFS来构造失败指针，与KMP算法相似的思想。
>
> 首先，root入队，第1次循环时处理与root相连的字符，也就是各个单词的第一个字符h和s，因为第一个字符不匹配需要重新匹配，所以第一个字符都指向root（root是Trie入口，没有实际含义）失败指针的指向对应下图中的(1)，(2)两条虚线；第2次进入循环后，从队列中先弹出h，接下来p指向h节点的fail指针指向的节点，也就是root；p=p->fail也就是p=NULL说明匹配序列为空，则把节点e的fail指针指向root表示没有匹配序列，对应图-2中的(3)，然后节点e进入队列；第3次循环时，弹出的第一个节点a的操作与上一步操作的节点e相同，把a的fail指针指向root，对应图-2中的(4)，并入队；第4次进入循环时，弹出节点h(图中左边那个)，这时操作略有不同。由于p->next[i]!=NULL(root有h这个儿子节点，图中右边那个)，这样便把左边那个h节点的失败指针指向右边那个root的儿子节点h，对应图-2中的(5)，然后h入队。以此类推：在循环结束后，所有的失败指针就是图-2中的这种形式。
>
> ![](6.png)
>
> **三、扫描**
>
> 构造好Trie和失败指针后，我们就可以对主串进行扫描了。这个过程和KMP算法很类似，但是也有一定的区别，主要是因为AC自动机处理的是多串模式，需要防止遗漏某个单词，所以引入temp指针。
>
> 匹配过程分两种情况：(1)当前字符匹配，表示从当前节点沿着树边有一条路径可以到达目标字符，此时只需沿该路径走向下一个节点继续匹配即可，目标字符串指针移向下个字符继续匹配；(2)当前字符不匹配，则去当前节点失败指针所指向的字符继续匹配，匹配过程随着指针指向root结束。重复这2个过程中的任意一个，直到模式串走到结尾为止。
>
>  对照上图，看一下模式匹配这个详细的流程，其中模式串为yasherhs。对于i=0,1。Trie中没有对应的路径，故不做任何操作；i=2,3,4时，指针p走到左下节点e。因为节点e的count信息为1，所以cnt+1，并且讲节点e的count值设置为-1，表示改单词已经出现过了，防止重复计数，最后temp指向e节点的失败指针所指向的节点继续查找，以此类推，最后temp指向root，退出while循环，这个过程中count增加了2。表示找到了2个单词she和he。当i=5时，程序进入第5行，p指向其失败指针的节点，也就是右边那个e节点，随后在第6行指向r节点，r节点的count值为1，从而count+1，循环直到temp指向root为止。最后i=6,7时，找不到任何匹配，匹配过程结束。
>
> （以上转自:http://blog.csdn.net/niushuai666/article/details/7002823）

​	

动态转移方程：

​	f\[i]\[j]=max(f\[i-1]\[j可能的上一个状态])+End[j]

​	*i是长度，j是在AC自动机中的节点*

​	但是，一般的矩阵快速幂都只能用来完成线性递推，通常是用来解决d\[i]\[j]=A*d\[i-1]\[a1]+B*d\[i-2]\[a2]+...类似的问题，而这题需要维护最大值。

​	这里就要讲到了矩阵乘法的变形了。之所以矩阵乘法是可行的是因为乘法的结合律，但是加法和max()同样有结合律。我们再回顾一下矩阵快速幂的另外一道经典题，告诉你一个有向图，可能有重边，告诉你起点和终点，要你恰好k步从起点走到终点，有多少种方法。最后，我们的dp方程是这样写的：f\[i]\[j]=sigma(f\[i]\[k]*f\[k]\[j])。这个题的答案就只要拿邻接矩阵求幂，然后把第0列的数字加起来就行了。

​	我们如果把sigma改成max，把乘号改成加号，那么就和这道题的方程完全一样了。对于这道题，我们只需要对矩阵乘法的定义稍作修改，就能用来优化取最大值的dp了。

## 3、程序

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long LL;
typedef pair<int,int> PII;
typedef vector<LL> vec;
typedef vector<vec> mat;

const int maxn=10007;
const LL INF=0x3f3f3f3f3f3f3f3f;

char S[maxn];
int rear,root,Next[maxn][26],Fail[maxn],End[maxn];

int New(){
    rear++;
    End[rear]=0;
    for(int i=0;i<26;i++){
        Next[rear][i]=-1;
    }
    return rear;
}

void Init(){
    rear=0;
    root=New();
}

void Add(char *A){
    int n=strlen(A),now=root;
    for(int i=0;i<n;i++){
        int id=A[i]-'a';
        if(Next[now][id]==-1){
            Next[now][id]=New();
        }
        now=Next[now][id];
    }
    End[now]++;
}

void mat_fill(mat &A,LL val){
    for(int i=0;i<A.size();i++){
        for(int j=0;j<A[0].size();j++){
            A[i][j]=val;
        }
    }
}

mat Build(){
    queue<int>Q;
    Fail[root]=root;
    for(int i=0;i<26;i++){
        if(Next[root][i]==-1){
            Next[root][i]=root;
        } else{
            Fail[Next[root][i]]=root;
            Q.push(Next[root][i]);
        }
    }
    while(!Q.empty()){
        int u=Q.front();Q.pop();
        End[u]+=End[Fail[u]];
        for(int i=0;i<26;i++){
            if(Next[u][i]==-1){
                Next[u][i]=Next[Fail[u]][i];
            } else{
                Fail[Next[u][i]]=Next[Fail[u]][i];
                Q.push(Next[u][i]);
            }
        }
    }
    mat A(rear,vec(rear));
    mat_fill(A,-INF);
    for(int i=1;i<=rear;i++){
        for(int j=0;j<26;j++){
            int chd=Next[i][j];
            A[chd-1][i-1]=End[chd];
        }
    }
    return A;
}

mat mat_mul(mat &A,mat &B){
    mat C(A.size(),vec(B[0].size()));
    mat_fill(C,-INF);
    for(int i=0;i<A.size();i++){
        for(int j=0;j<B[0].size();j++){
            for(int k=0;k<B.size();k++){
                if(A[i][k]+B[k][j] >= 0){
                    C[i][j]=max(C[i][j],A[i][k]+B[k][j]);
                }
            }
        }
    }
    return C;
}

mat mat_pow(mat A,LL n){
    mat B=A;n--;
    while(n){
        if(n&1) B=mat_mul(B,A);
        A=mat_mul(A,A);
        n>>=1;
    }
    return B;
}

void print(mat &A){
    for(int i=0;i<A.size();i++){
        for(int j=0;j<A[0].size();j++){
            fuck(A[i][j]);
        } printf("\n");
    }
}

int main(){
    int n;
    LL m;
    scanf("%d%lld",&n,&m);
    Init();
    for(int i=1;i<=n;i++){
        scanf("%s",S);
        Add(S);
    }
    mat A=Build();
    A=mat_pow(A,m);
    LL ans=0;
    for(int i=0;i<rear;i++){
        ans=max(ans,A[i][0]);
    }
    printf("%lld\n",ans);
    return 0;
}
```



---

# CCF-CSP 201512-4 送货

## 1、 题目

**问题描述**

　　为了增加公司收入，F公司新开设了物流业务。由于F公司在业界的良好口碑，物流业务一开通即受到了消费者的欢迎，物流业务马上遍及了城市的每条街道。然而，F公司现在只安排了小明一个人负责所有街道的服务。
　　任务虽然繁重，但是小明有足够的信心，他拿到了城市的地图，准备研究最好的方案。城市中有n个交叉路口，m条街道连接在这些交叉路口之间，每条街道的首尾都正好连接着一个交叉路口。除开街道的首尾端点，街道不会在其他位置与其他街道相交。每个交叉路口都至少连接着一条街道，有的交叉路口可能只连接着一条或两条街道。
　　小明希望设计一个方案，从编号为1的交叉路口出发，每次必须沿街道去往街道另一端的路口，再从新的路口出发去往下一个路口，直到所有的街道都经过了正好一次。

**输入格式**

　　输入的第一行包含两个整数n, m，表示交叉路口的数量和街道的数量，交叉路口从1到n标号。
　　接下来m行，每行两个整数a, b，表示和标号为a的交叉路口和标号为b的交叉路口之间有一条街道，街道是双向的，小明可以从任意一端走向另一端。两个路口之间最多有一条街道。

**输出格式**

　　如果小明可以经过每条街道正好一次，则输出一行包含m+1个整数p1, p2, p3, ..., pm+1，表示小明经过的路口的顺序，相邻两个整数之间用一个空格分隔。如果有多种方案满足条件，则输出字典序最小的一种方案，即首先保证p1最小，p1最小的前提下再保证p2最小，依此类推。
　　如果不存在方案使得小明经过每条街道正好一次，则输出一个整数-1。

**样例输入**

​	4 5
​	1 2
​	1 3
​	1 4
​	2 4
​	3 4

**样例输出**

​	1 2 4 1 3 4

**样例说明**

　　城市的地图和小明的路径如下图所示。
![img](http://118.190.20.162/RequireFile.do?fid=HgNYQ5G9)

**样例输入**

​	4 6
​	1 2
​	1 3
​	1 4
​	2 4
​	3 4
​	2 3

**样例输出**

​	-1

**样例说明**

　　城市的地图如下图所示，不存在满足条件的路径。
![img](http://118.190.20.162/RequireFile.do?fid=67NLAqAY)

**评测用例规模与约定**

　　前30%的评测用例满足：1 ≤ n ≤ 10, n-1 ≤ m ≤ 20。
　　前50%的评测用例满足：1 ≤ n ≤ 100, n-1 ≤ m ≤ 10000。
　　所有评测用例满足：1 ≤ n ≤ 10000，n-1 ≤ m ≤ 100000。

## 2、 题解

一笔画问题：**欧拉路径+字典序最小**

**欧拉路径（无向图）**

​        无向图：当且仅当该图所有顶点的度数为偶数 或者 除了两个度数为奇数外其余的全是偶数。
​        有向图：当且仅当该图所有顶点 出度=入度 或者 一个顶点 出度=入度+1，另一个顶点 入度=出度+1，其他顶点 出度=入度。

步骤：

1. 判断图是否是欧拉路径
2. 用字典序最小输出路径

## 3、 程序

```c++
#include <cstdio>  
#include <vector>  
#include <cstring>  
#include <iostream>  
#include <algorithm>  
using namespace std;  
const int maxn=1e4+50;  
const int maxm=2e5+50;  

struct Edge{  
    int adj,next,v;  
}e[maxm];  

struct Node{  
    int x,y,sum;  
}p[maxm];  

bool v[maxn];  
vector<int> ans; 
int n,m,odd,tot=1,nt,degree[maxn],head[maxn]; 

void adda (int x,int y){  
    e[++tot].adj=y;
    e[tot].next=head[x];
    head[x]=tot;  
    e[++tot].adj=x;
    e[tot].next=head[y];
    head[y]=tot;  
}  

void dfs (int u){  
	int i;
    v[u]=1;  
    for(i=head[u];i;i=e[i].next){  
        if(!e[i].v&&!e[i^1].v){  
            nt=e[i].adj;  
            e[i].v=1;  
            e[i^1].v=1;  
            dfs (nt);  
        }  
    }  
    ans.push_back(u); 
}  

int cmp (Node a,Node b){  
    return a.sum>b.sum;  
}  

void init (){
	int i;
    scanf ("%d%d",&n,&m);  
    for (i=1;i<=m;i++){  
        scanf("%d%d",&p[i].x,&p[i].y);  
        p[i].sum=p[i].x+p[i].y;  
        degree[p[i].x]++;
        degree[p[i].y]++;  
    }  
    for(i=1;i<=n;i++)
        if(degree[i]&1) odd++; 
    sort (p+1,p+m+1,cmp);  
    for (i=1;i<=m;i++)  
        adda (p[i].x,p[i].y); 
}

int main(){ 
    int i,co=0;   
	init ();
    dfs (1);  
    for(i=1;i<=n;i++)  
        if(!v[i]) co=1;  
    if(!(odd==0||odd==2)||co) printf("-1\n");  
    else{  
        for(i=ans.size()-1;i>0;i--)  //倒着输出就是最小字典序 
            printf("%d ",ans[i]);  
        printf("%d\n",ans[0]);  
    }  
    return 0;  
}
```



---

# CCF-CSP 201604-4游戏

## 1、 题目

**问题描述**　　

​	小明在玩一个电脑游戏，游戏在一个n×m的方格图上进行，小明控制的角色开始的时候站在第一行第一列，目标是前往第n行第m列。
　　方格图上有一些方格是始终安全的，有一些在一段时间是危险的，如果小明控制的角色到达一个方格的时候方格是危险的，则小明输掉了游戏，如果小明的角色到达了第n行第m列，则小明过关。第一行第一列和第n行第m列永远都是安全的。
　　每个单位时间，小明的角色必须向上下左右四个方向相邻的方格中的一个移动一格。
　　经过很多次尝试，小明掌握了方格图的安全和危险的规律：每一个方格出现危险的时间一定是连续的。并且，小明还掌握了每个方格在哪段时间是危险的。
　　现在，小明想知道，自己最快经过几个时间单位可以达到第n行第m列过关。

**输入格式**　　

​	输入的第一行包含三个整数n, m, t，用一个空格分隔，表示方格图的行数n、列数m，以及方格图中有危险的方格数量。
　　接下来t行，每行4个整数r, c, a, b，表示第r行第c列的方格在第a个时刻到第b个时刻之间是危险的，包括a和b。游戏开始时的时刻为0。输入数据保证r和c不同时为1，而且当r为n时c不为m。一个方格只有一段时间是危险的（或者说不会出现两行拥有相同的r和c）。

**输出格式**　　

​	输出一个整数，表示小明最快经过几个时间单位可以过关。输入数据保证小明一定可以过关。

**样例输入**

​	3 3 3
​	2 1 1 1
​	1 3 2 10
​	2 2 2 10

**样例输出**

​	6

**样例说明**　　

​	第2行第1列时刻1是危险的，因此第一步必须走到第1行第2列。
　　第二步可以走到第1行第1列，第三步走到第2行第1列，后面经过第3行第1列、第3行第2列到达第3行第3列。

**评测用例规模与约定**　　

​	前30%的评测用例满足：0 < n, m ≤ 10，0 ≤ t < 99。
　　所有评测用例满足：0 < n, m ≤ 100，0 ≤ t < 9999，1 ≤ r ≤ n，1 ≤ c ≤ m，0 ≤ a ≤ b ≤ 100。

## 2、 题解

　　该题目用bfs计算出每个时刻小明可以在的位置， 小明可以上下左右移动， 对于下一步的移动， 排除过界的情况， 排除目标点处在危险时间的情况， 排除目标点已经在队列中的情况， 进行bfs， 当到达（n， m）时立即返回当前步数。

​	测试用例范围不大， 暴力bfs并不会超出时间限制。

## 3、 程序

```c++
#include <iostream>
#include <stdio.h>
#include <stdlib.h>
using namespace std;

int n,m,t,l[107][107],r[107][107],addx[4]={-1,1,0,0},addy[4]={0,0,-1,1};
bool vis[107][107][107];

struct node{
	int x,y,time;
}q[1000007];

void init (){
	int x,y,a,b;
	cin>>n>>m>>t;
	while (t--){
		scanf ("%d%d%d%d",&x,&y,&a,&b);
		l[x][y]=a;r[x][y]=b;
	}
}

void work (){
	int i,tx,ty,tt,head,tail;
	node u;
	head=1;tail=1;
	q[1]={1,1,0};
	while (head<=tail){
		u=q[head++];
		vis[u.x][u.y][u.time]=0;
		for (i=0;i<4;i++){
			tx=u.x+addx[i];
			ty=u.y+addy[i];
			tt=u.time+1;
			if (tx==n&&ty==m)break;
			if (tx>=1&&tx<=n&&ty>=1&&ty<=m&&(tt<l[tx][ty]||tt>r[tx][ty])&&!vis[tx][ty][tt]){
				q[++tail]={tx,ty,tt};
				vis[tx][ty][tt]=1;
			}
		}
		if (i<4)break;
	}
	cout<<tt<<endl;
}

int main (){
	init ();
	work ();
	return 0;
}
```



---

# CCF-CSP 201609-4交通规则

## 1、 题目

**问题描述**

　　G国国王来中国参观后，被中国的高速铁路深深的震撼，决定为自己的国家也建设一个高速铁路系统。
　　建设高速铁路投入非常大，为了节约建设成本，G国国王决定不新建铁路，而是将已有的铁路改造成高速铁路。现在，请你为G国国王提供一个方案，将现有的一部分铁路改造成高速铁路，使得任何两个城市间都可以通过高速铁路到达，而且从所有城市乘坐高速铁路到首都的最短路程和原来一样长。请你告诉G国国王在这些条件下最少要改造多长的铁路。

**输入格式**

　　输入的第一行包含两个整数n, m，分别表示G国城市的数量和城市间铁路的数量。所有的城市由1到n编号，首都为1号。
　　接下来m行，每行三个整数a, b, c，表示城市a和城市b之间有一条长度为c的双向铁路。这条铁路不会经过a和b以外的城市。

**输出格式**

　　输出一行，表示在满足条件的情况下最少要改造的铁路长度。

**样例输入**

```
4 5
1 2 4
1 3 5
2 3 2
2 4 3
3 4 2
```

**样例输出**

```
11
```

**评测用例规模与约定**

　　对于20%的评测用例，1 ≤ n ≤ 10，1 ≤ m ≤ 50；
　　对于50%的评测用例，1 ≤ n ≤ 100，1 ≤ m ≤ 5000；
　　对于80%的评测用例，1 ≤ n ≤ 1000，1 ≤ m ≤ 50000；
　　对于100%的评测用例，1 ≤ n ≤ 10000，1 ≤ m ≤ 100000，1 ≤ a, b ≤ n，1 ≤ c ≤ 1000。输入保证每个城市都可以通过铁路达到首都。

## 2、题解

### 1. Dijkstra算法 

1. 定义概览

   ​	Dijkstra(迪杰斯特拉)算法是典型的单源最短路径算法，用于计算一个节点到其他所有节点的最短路径。主要特点是以起始点为中心向外层层扩展，直到扩展到终点为止。Dijkstra算法是很有代表性的最短路径算法，在很多专业课程中都作为基本内容有详细的介绍，如数据结构，图论，运筹学等等。注意该算法要求图中不存在负权边。

   ​	问题描述：在无向图 G=(V,E) 中，假设每条边 E[i] 的长度为 w[i]，找到由顶点 V0 到其余各点的最短路径。（单源最短路径）


2. 算法描述

   1)算法思想：

   ​	设G=(V,E)是一个带权有向图，把图中顶点集合V分成两组，第一组为已求出最短路径的顶点集合（用S表示，初始时S中只有一个源点，以后每求得一条最短路径 , 就将加入到集合S中，直到全部顶点都加入到S中，算法就结束了），第二组为其余未确定最短路径的顶点集合（用U表示），按最短路径长度的递增次序依次把第二组的顶点加入S中。在加入的过程中，总保持从源点v到S中各顶点的最短路径长度不大于从源点v到U中任何顶点的最短路径长度。此外，每个顶点对应一个距离，S中的顶点的距离就是从v到此顶点的最短路径长度，U中的顶点的距离，是从v到此顶点只包括S中的顶点为中间顶点的当前最短路径长度。

   2)算法步骤：

   ​	a.初始时，S只包含源点，即S＝{v}，v的距离为0。U包含除v外的其他顶点，即:U={其余顶点}，若v与U中顶点u有边，则<u,v>正常有权值，若u不是v的出边邻接点，则<u,v>权值为∞。

   ​	b.从U中选取一个距离v最小的顶点k，把k，加入S中（该选定的距离就是v到k的最短路径长度）。

   ​	c.以k为新考虑的中间点，修改U中各顶点的距离；若从源点v到顶点u的距离（经过顶点k）比原来距离（不经过顶点k）短，则修改顶点u的距离值，修改后的距离值的顶点k的距离加上边上的权。

   ​	d.重复步骤b和c直到所有顶点都包含在S中。


3. 执行动画过程如下图

![img](http://pic002.cnblogs.com/images/2012/426620/2012073019540660.gif)

### 2. 本题分析

​	“所有城市乘坐高速铁路到首都的最短路程和原来一样长” ，结果满足单源最短路径；“最少要改造多少铁路”，说明是要在最短路径中找最小花费。

​	

　　![](2.png)

​	如上图所示，点1到点3的最短路径是4，要连通点3，1-2-3、1-3和1-4-3都是最短路径。但是如果选1-3，需要增加的铁轨为4个单位（蓝色）；选1-2-3需要增加的铁轨为2个单位（红色）；而选1-4-3的话，需要增加的铁轨只有1个单位（绿色）。所以此时新增点3代价最小的应该选最后一种方案。 

​	该题在Dijkstra基础上添加一下更新操作（即在最短路的前提下满足最小代价）即可。

- cost[v]：表示新增连通v点的代价（所增加的边的权）比如上图中cost[3]=1。

- dis[v]：表示从点1到点v的最短路径，如dis[3]=4。

  ​	核心：当u,v为邻点，dist[v]==dist[u]+cost(u,v) 时，cost[v]=min(cost[v],cost(u,v))，这样最终得到的sum{cost[v]}就是满足最短路径条件下的最小花费。


## 3、程序

```c++
#include <iostream>
#include <cstring>
#include <cstdio>
#include <algorithm>
#include <vector>
#include <queue>
using namespace std;

int n,m,ans,tot,q[100007],head[100007],dis[100007],cost[100007];
bool vis[100007];

struct edge{
    int adj,w,next;
}e[500007];

void add (int u,int v,int w){	//添加边
    tot++;
    e[tot].adj=v;e[tot].w=w;e[tot].next=head[u];
    head[u]=tot;
}

void init (){	//输入，添加图的边 
    int i,a,b,c;
    cin>>n>>m;
    for(i=1;i<=m;i++){
        scanf ("%d%d%d",&a,&b,&c);
        add (a,b,c);
        add (b,a,c);
    }
}

void dij(){
    int i,j,he,tail,mini,u,v,tw,t;
    memset (dis,0x3f,sizeof dis);
    memset (cost,0x3f,sizeof cost);
    dis[1]=cost[1]=0;
    q[1]=1;
    he=1;tail=1;
    while (he<=tail){
        mini=he;
        for (j=he+1;j<=tail;j++){	//求出队内距离最小的点
            if (dis[q[j]]<dis[q[mini]])mini=j;
        }
        t=q[mini];q[mini]=q[he];q[he]=t;
        u=q[he++];
        
        if (vis[u])continue;
        vis[u]=1;
        for (j=head[u];j;j=e[j].next){	//更新
            v=e[j].adj;
            if (vis[v])continue;
            tw=e[j].w;	//tw为从u到v点的边的权
            if (dis[v]-tw>dis[u]){
                dis[v]=dis[u]+tw;
                cost[v]=tw;	
                q[++tail]=v;
            }
            else if(dis[v]-tw==dis[u]&&cost[v]>tw){
                cost[v]=tw;
            }
        }
    }
    for (i=2;i<=n;i++){	//计算答案
        ans+=cost[i];
    }
    cout<<ans<<endl;
}

int main (){
    init ();
    dij ();
    return 0;
}
```



# CCF-CSP 201612-4压缩编码

##1、题目
**问题描述**

　　给定一段文字，已知单词a1, a2, …, an出现的频率分别t1, t2, …, tn。可以用01串给这些单词编码，即将每个单词与一个01串对应，使得任何一个单词的编码（对应的01串）不是另一个单词编码的前缀，这种编码称为前缀码。
　　使用前缀码编码一段文字是指将这段文字中的每个单词依次对应到其编码。一段文字经过前缀编码后的长度为：
　　L=a1的编码长度×t1+a2的编码长度×t2+…+ an的编码长度×tn。
　　定义一个前缀编码为字典序编码，指对于1 ≤ i < n，ai的编码（对应的01串）的字典序在ai+1编码之前，即a1, a2, …, an的编码是按字典序升序排列的。
　　例如，文字E A E C D E B C C E C B D B E中， 5个单词A、B、C、D、E出现的频率分别为1, 3, 4, 2, 5，则一种可行的编码方案是A:000, B:001, C:01, D:10, E:11，对应的编码后的01串为1100011011011001010111010011000111，对应的长度L为3×1+3×3+2×4+2×2+2×5=34。
　　在这个例子中，如果使用哈夫曼(Huffman)编码，对应的编码方案是A:000, B:01, C:10, D:001, E:11，虽然最终文字编码后的总长度只有33，但是这个编码不满足字典序编码的性质，比如C的编码的字典序不在D的编码之前。
　　在这个例子中，有些人可能会想的另一个字典序编码是A:000, B:001, C:010, D:011, E:1，编码后的文字长度为35。
　　请找出一个字典序编码，使得文字经过编码后的长度L最小。在输出时，你只需要输出最小的长度L，而不需要输出具体的方案。在上面的例子中，最小的长度L为34。

**输入格式**

　　输入的第一行包含一个整数n，表示单词的数量。
　　第二行包含n个整数，用空格分隔，分别表示a1, a2, …, an出现的频率，即t1, t2, …, tn。请注意a1, a2, …, an具体是什么单词并不影响本题的解，所以没有输入a1, a2, …, an。

**输出格式**

　　输出一个整数，表示文字经过编码后的长度L的最小值。

**样例输入**

​	5
​	1 3 4 2 5

**样例输出**

​	34

**样例说明**

　　这个样例就是问题描述中的例子。如果你得到了35，说明你算得有问题，请自行检查自己的算法而不要怀疑是样例输出写错了。

**评测用例规模与约定**

　　对于30%的评测用例，1 ≤ n ≤ 10，1 ≤ ti ≤ 20；
　　对于60%的评测用例，1 ≤ n ≤ 100，1 ≤ ti ≤ 100；
　　对于100%的评测用例，1 ≤ n ≤ 1000，1 ≤ ti ≤ 10000。][1]

##2、题解
###1. 引——石子合并问题
1. 题目描述：在一个操场上摆放着一行共n堆的石子。现要将石子有序地合并成一堆。规定每次只能选相邻的两堆合并成新的一堆，并将新的一堆石子数记为该次合并的得分。请计算出将n堆石子合并成一堆的最小得分。

2. 题解：
3. 人们的第一想法往往是贪心（找最大/最小的堆合并），但是很容易找到贪心的反例：
-  贪心：
    3  4  6  5 4  2
    5  4  6  5 4        得分：5
    9  6  5 4           得分：9
    9  6 9              得分：9
    15 9                得分：15
    24                  得分：24
    总得分：62
-  合理方案：
                3  4  6  5 4  2
                7  6  5  4 2        得分：7
                7  6  5 6           得分：6
                7  11 6             得分：11
                13 11               得分：13
                24                  得分：24
                总得分：61

   2. ​显然利用贪心来做是错误的，贪心算法在子过程中得出的解只是局部最优,而不能保证使得全局的值最优。
       如果N－1次合并的全局最优解包含了每一次合并的子问题的最优解，那么经这样的N－1次合并后的得分总和必然是最优的。因此我们需要通过动态规划算法来求出最优解。
       在此我们假设有n堆石子，一字排开，合并相邻两堆的石子，每合并两堆石子得到一个分数，最终合并后总分数最少的。
       我们设$dp[i][j]$定义为第i堆石子到第j堆石子合并后的最少总分数，$sum[i$][j]为第i堆到第j堆石子的总数，$a[i]$为第i堆石子得石子数量。
       当合并的石子堆为1堆时，很明显$dp[i][i]$的分数为0;
       当合并的石子堆为2堆时，$dp[i][i+1]$的分数为$a(i)+a(i+1)$;
       当合并的石子堆为3堆时，$dp[i$][i+2]的分数为$Min((dp[i][i]+dp[i+1][i+2]+sum[i][i+2]),(dp[i][i+1]+dp[i+2][i+2]+sum[i][i+2]))$;
       ​当合并的石子堆为4堆时......
       可总结出动态转移方程：**$dp[i][j] = min{ dp[i][k]+dp[k+1][j]+sum[i][j] }  (i <= k < j)$**

###2. 压缩编码
1. 问题分析：

   ​	首先要清楚huffman编码的原理，然后要在huffman的基础上保证有序，所以合并过程从huffman的合并最小的两个变成了合并相邻两个让其总和最小，转化成石子合并问题，每堆石子的数量相当于字母出现的频率。

   时间复杂度:O（n^3），理论上可以得60分 （实际上，如果CCF的数据比较弱的话可以得满分，亲测2.437s过）。

2. 时间优化：

   四边形优化
   **详细证明：[传送门][2]**
   设$m[i,j]$表示动态规划的状态量。
   $m[i,j]$有类似如下的状态转移方程：
   $m[i,j]=min{m[i,k]+m[k,j]}(i≤k≤j)$
   m满足四边形不等式是适用这种优化方法的必要条件
   定义$s(i,j)$为函数$m(i,j)$对应的使得m(i,j)取得最大值的k值。
   我们可以证明，$s[i,j-1]≤s[i,j]≤s[i+1,j] $ 
   那么改变状态转移方程为：
   **m[i,j]=min{m[i,k]+m[k,j]} (s[i,j-1]≤k≤s[i+1,j])**
   时间复杂度:$O(n^2)$，可以得100分（亲测46ms过）。

##3、程序
```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

typedef long long LL;
const int MAXN=1007;
const LL oo=(1LL<<60);

int n,s[MAXN][MAXN];
LL a[MAXN],dp[MAXN][MAXN],sum[MAXN];

int main() {
    int i,len,j,k,mink;
    LL tmp;
    scanf("%d",&n);
    for(i=1;i<=n;i++) 
        scanf("%lld",&a[i]);
    sum[0]=0;
    for(i=1;i<=n;i++){
        sum[i]=sum[i-1]+a[i];
        s[i][i]=i;
    }
    for(len=1;len<n;len++) {
        for(i=1;i+len<=n;i++) {
            j=i+len;
            tmp=oo;
            mink=i;
            for(k=s[i][j-1];k<=s[i+1][j];k++) {
                if(dp[i][k]+dp[k+1][j]+sum[j]-sum[i-1]<tmp){
                    tmp=dp[i][k]+dp[k+1][j]+sum[j]-sum[i-1];
                    mink=k;
                }
            }
            dp[i][j]=tmp;
            s[i][j]=mink;
        }
    }
    printf("%lld\n",dp[1][n]);
    return 0;
}
```


[1]: http://115.28.138.223/view.page?gpid=T49
[2]: http://baike.baidu.com/view/1985058.htm?fr=aladdin