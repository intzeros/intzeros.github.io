---
layout: post
title:  "计算几何"
date:   2017-04-27 09:16:00
categories: Algorithm
mathjax: true
---

## 点和线
### 基础
向量  $\vec{a} = (x_1, y_1), \vec{b} = (x_2, y_2)$.

(1) **点积**:  $$\vec{a}\cdot \vec{b} = x_1x_2 + y_1y_2 = \vert a \vert \vert b \vert cos\theta$$

(2) **叉积** (cross product):

<img src="http://wp.lszero.com/wp-content/uploads/2017/04/Screen-Shot-2017-04-27-at-11.20.09-AM.png" style="zoom:50%; float:right" />

$$\vert \vec{a}\times \vec{b} \vert = det\begin{bmatrix}x_1 & x_2 \\y_1 & y_2 \end{bmatrix}= x_1y_2 - x_2y_1 = \vert a \vert \vert b \vert sin\theta$$.

$\vec{a}\times \vec{b} =  - \vec{b}\times \vec{a}$

看作由点(0, 0), $\vec{a}$, $\vec{b}$ 和 $\vec{a}+\vec{b}=(x_1+x_2, y_1+y_2)$所形成的平行四边形的有向面积. 

**右手**定则判断方向.

(3) 向量 $\vec{a}, \vec{b}$ **共线**:  $\vec{a}\times \vec{b} = x_1y_2 - x_2y_1 = 0$

向量 $\vec{a}, \vec{b}$ **垂直**:  $\vec{a}\cdot \vec{b} = x_1x_2 + y_1y_2 = 0$

(4) 点 $P_1 = (x_1, y_1), P2 = (x_2, y_2)$, 则 $\overrightarrow{P_1P_2} = (x_2-x_1, y_2-y_1)$.

(5) 判断三点共线: `叉乘==0`.

(6) 判断点P在线段$P_1P_2$上: 

`叉乘==0 && (P1.x-P.x)*(P2.x-P.x)<0 && (P1.y-P.y)*(P2.y-P.y)<0`

(7) 判断两直线平行: `叉乘==0`.

<!-- more -->

### 点相对有向线段的方向

(1) 判断点P2相对于有向线段$\overrightarrow{P_0P_1}$ 的方向: 

通过计算$\overrightarrow{P_0P_1}\times \overrightarrow{P_0P_2}$的结果为正还是为负.

(2) 在P1处，两条连续的有向线段$\overrightarrow{P_0P_1}$ 和$\overrightarrow{P_1P_2}$ 是向左转还是向右转: 同上述问题(1).

<img src="http://wp.lszero.com/wp-content/uploads/2017/04/Screen-Shot-2017-04-27-at-12.27.01-PM.png" style="zoom:50%" />

(3) 判断两条线段相交:
点P1和点P2分别在$\overrightarrow{P_3P_4}$的两个不同方向 **&&** 点P3和点P4分别在$\overrightarrow{P_1P_2}$的两个不同方向.
特殊情况: 仅有一个端点在另一条线段上.

<img src="http://wp.lszero.com/wp-content/uploads/2017/04/Screen-Shot-2017-04-27-at-12.53.42-PM.png" style="zoom:25%" />

(4) 判断线段与直线相交: 同理.

### 常用代码模板

```c++
// 计算 向量p0p1 叉乘 向量p0p2
double xmulti(Point p0, Point p1, Point p2){
    return (p1.x-p0.x)*(p2.y-p0.y) - (p2.x-p0.x)*(p1.y-p0.y);
}
```

```c++
// 判断三点共线
bool collinear(Point p1, Point p2, Point p3){
    double rst = xmulti(p1, p2, p3);
    return fabs(rst) < 1e-8;
}
```

```c++
// 判断点p是否在线段p1p2上(包括端点)
bool dot_online(Point p, Point p1, Point p2){
    double d = xmulti(p, p1, p2);
    return fabs(d) < 1e-8 && (p1.x-p.x)*(p2.x-p.x)<0 && (p1.y-p.y)*(p2.y-p.y)<0;
}
```

``` c++
// 判断两条线段相交(包括仅有一个端点在另一条线段上的情况)
bool intersection(Point p1, Point p2, Point p3, Point p4){
    double d1 = xmulti(p3, p4, p1); // p1相对于p3p4
    double d2 = xmulti(p3, p4, p2);
    double d3 = xmulti(p1, p2, p3);
    double d4 = xmulti(p1, p2, p4);
    if(d1*d2 < 0 && d3*d4 < 0) return true;
    else if(fabs(d1) < 1e-8 && dot_online(p1, p3, p4)) return true;
    else if(fabs(d2) < 1e-8 && dot_online(p2, p3, p4)) return true;
    else if(fabs(d3) < 1e-8 && dot_online(p3, p1, p2)) return true;
    else if(fabs(d4) < 1e-8 && dot_online(p4, p1, p2)) return true;
    return false;
}
```



## 凸包 Convex Hull

### Graham扫描法求凸包

**时间复杂度: O(nlogn)** (主要是排序)
空间复杂度: stack.

---

过程: (poj 1113 Wall)
<img src="https://upload.wikimedia.org/wikipedia/commons/7/71/GrahamScanDemo.gif" style="zoom:50%; float:right" />

(1) 确定坐标原点 v[0]: 选 y值 最小的点.

(2) 将剩余点按 **极角** 排序, 对极角相同的点按据v[0]的远近排序.
按 **极角** 排序, 即按 **叉乘**的值 排序: 
若 v[0]v[j] × v[0]v[i] > 0, 即意味着 点v[j]在v[0]v[i]的顺时针方向. 在 cmp函数中, 因为返回值为正, 也就把 v[i]排在 v[j]之前.

(3) v[0], v[1] 入栈.

(4) 依次扫描之后遇到的每个点 v[i]:
​	若stack.size()>=2, 检查 v[i] 相对于栈顶两个点构成的有向线段的方向.

(5) 栈中元素即求出的凸包顶点.

---

代码:

```c++
vector<Point> v;

// 计算 向量p0p1 叉乘 向量p0p2
double xmulti(const Point &p0, const Point &p1, const Point &p2){
    return (p1.x-p0.x)*(p2.y-p0.y) - (p2.x-p0.x)*(p1.y-p0.y);
}

double dis(const Point &p1, const Point &p2){
    return (p1.x-p2.x)*(p1.x-p2.x) + (p1.y-p2.y)*(p1.y-p2.y);
}

bool cmp(const Point &p1, const Point &p2){
    double d = xmulti(v[0], p1, p2);
    if(fabs(d) < 1e-8) return dis(v[0], p1) < dis(v[0], p2);
    else if(d > 0) return false;
    return true;
}

vector<Point> Graham(vector<Point> &v){
    vector<Point> stk;
    stk.push_back(v[0]);
    stk.push_back(v[1]);
    for(int i = 2; i < v.size(); i++){
        while(stk.size() >= 2 && xmulti(stk[stk.size()-2], stk[stk.size()-1], v[i]) > 0){
            stk.pop_back();
        }
        stk.push_back(v[i]); 
    }
    return stk;
}

int main(){
    // cin...
    double min = 1e9;
    int pos = 0;
    for(int i = 0; i < n; i++){
        if(v[i].y < min) min = v[i].y, pos = i;
    }

    swap(v[0], v[pos]);
    sort(v.begin()+1, v.end(), cmp);

    vector<Point> rst = Graham(v);
    return 0;
}
```

### 凸包面积

(1) 三角形面积

```c++
// 计算三角形面积, 输入三顶点
double area_triangle(Point p1, Point p2, Point p3){
    return fabs(xmult(p1,p2,p3))/2; 
}
```

```c++
// 计算三角形面积, 输入三边长
double area_triangle(double a, double b, double c){
    double s=(a+b+c)/2;
    return sqrt(s*(s-a)*(s-b)*(s-c));
}
```

(2) 凸包面积 (poj 3348 Cows)

```c++
// 计算多边形面积, 顶点按顺时针或逆时针给出
double area_polygon(vector<Point>& v){
    double rst = 0; 
    for(int i = 2; i < n; i++){
        rst += area_triangle(v[0], v[i-1], v[i]);
    }
    return rst;
}
```

## 最近点对

	求二维平面上的最近点对.
详见《编程之美》-> 分治思想.

​(​😀 不是说不贴代码就不重要…​😀 )

---

扩展问题:

	给定一个无序数组, 求排序后相邻两个元素的最大差值. (leetcode 164)
解法1: 分治

解法2: 桶排序

	求二维平面上的最远点对.
思路: 先求凸包, 后求凸包直径.

### 求凸包直径: 旋转卡壳算法 (Rotating Calipers)

<img src="http://lszero.com/wp-content/uploads/2017/04/2011040318073013.gif" style="zoom:50%; float:right" />

用两条平行线去卡住凸包, 被卡住的两个顶点称为[**对踵点对**](http://blog.csdn.net/acmaker/article/details/3561145). 

最远点对一定是对踵点对, 而对踵点对的个数为 O(n), 这就说明了解决问题的复杂度.

<img src="http://lszero.com/wp-content/uploads/2017/04/diam.gif" style="zoom:70%" />

一般情况下, 平行线是卡住两个顶点. 为了方便处理, 采用卡住一条边和一个顶点的方式. 

于是思路为, 枚举凸包上的所有边, 对每条边再找出距离该边最远的顶点, 这样看似是个O(n^2)的算法, 和直接枚举两个顶点没什么优势. 但是注意到当我们逆时针枚举边和点的时候, 因为对踵点到固定边的的距离比其他点到固定边的距离都要大, 而且是单调递增的, 所有没必要每次从头枚举, 而是接着上一次对踵点的位置开始.
<img src="http://lszero.com/wp-content/uploads/2017/04/appve.gif" />

代码: (参考 [here](http://www.cppblog.com/staryjy/archive/2009/11/19/101412.html))

```c++
//计算凸包直径，输入凸包ch，顶点个数为n，按逆时针排列，输出直径的平方
int rotating_calipers(Point *ch,int n)
{
    int q=1,ans=0;
    ch[n]=ch[0];
    for(int p=0;p<n;p++)
    {
        while(cross(ch[p+1],ch[q+1],ch[p])>cross(ch[p+1],ch[q],ch[p]))
            q=(q+1)%n;
        ans=max(ans,max(dist2(ch[p],ch[q]),dist2(ch[p+1],ch[q+1])));            
    }
    return ans; 
}
```

其中cross函数是计算叉积，可以想成是计算三角形面积，因为凸包上距离一条边最远的点和这条边的两个端点构成的三角形面积是最大的。之所以既要更新(ch[p],ch[q])又要更新(ch[p+1],ch[q+1])是为了处理凸包上两条边平行的特殊情况。

### 凸包最近点对

...

### 找最小圆覆盖平面所有点

...


## **三角剖分** 

待补充...