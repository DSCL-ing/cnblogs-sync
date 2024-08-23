[toc]



# AVL树

## AVL树的概念 

> 二叉搜索树虽可以缩短查找的效率，但如果**数据有序或接近有序二叉搜索树将退化为单支树**，查找元素相当于在顺序表中搜索元素，效率低下。因此，两位俄罗斯的数学家G.M.**A**delson-**V**elskii和E.M.**L**andis在1962年发明了一种解决上述问题的方法：当向二叉搜索树中插入新结点后，如果能保证每个结点的左右子树高度之差的绝对值不超过1(需要对树中的结点进行调整)，即可降低树的高度，从而减少平均搜索长度。
> 一棵AVL树或者是空树，或者是具有以下性质的二叉搜索树：
>
> - 它的左右子树都是AVL树 
>
> - 左右子树高度之差(简称平衡因子)的绝对值不超过1(-1/0/1)
>
>   ![image-20240821125504112](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240821125504112.png)
>
>   (默认平衡因子=右子树高度-左子树高度)
>
> **如果一棵二叉搜索树是高度平衡的，它就是AVL树。**如果它有n个结点，其高度可保持在$O(log_2 n)$，搜索时间复杂度O($log_2 n$)。



AVL树是由BST二叉搜索树改进而来,基本概念参考BST篇,本篇文章不再详细描述.



### AVL树节点的定义：

```
template<class K, class V> 
struct AVLTreeNode
{
		//三叉链: left right parent
     AVLTreeNode* _left;   // 该节点的左孩子 
     AVLTreeNode* _right;  // 该节点的右孩子 
     AVLTreeNode* _parent; // 该节点的双亲
     std::pair<K,V> _kv;	 // 键值对
     int _bf;              // 该节点的平衡因子 balance factor

		AVLTreeNode(const std::pair<K, V>& kv)
			:_left(nullptr)
			, _right(nullptr)
			, _parent(nullptr)
			, _kv(kv)
			, _bf(0)
		{}
};
```



### AVL树的插入



#### 基本情况分析

AVL树就是在二叉搜索树的基础上引入了平衡因子，因此AVL树也可以看成是二叉搜索树。那么AVL树的插入过程可以分为两步：
1. 按照二叉搜索树的方式插入新节点

2. 调整节点的平衡因子

   a. 更新父结点平衡因子

   b. 根据父结点的平衡因子进行相应的操作



对于平衡因子

插入新结点后,首先可能会**影响父结点的平衡因子**,迭代往上,可能还会影响部分或全部(到根节点)祖先结点的平衡因子.

具体地说,即插入新结点后,需要根据父结点平衡因子的情况,决定是否继续往上对祖结点进行更新平衡因子,最多到达根结点.



#### 平衡因子对应的操作

父结点平衡因子如何决定是否继续往上更新? 取决于更新后parent->_bf的值

1. 若`parent->_bf == 1 || parent->_bf == -1` ,说明插入前的父结点一定是左右子树高度相等,即\_bf为0.新增结点后父结点所在子树高度一定发生变化,爷爷结点所在子树也可能发生变化,因此需要进行**迭代更新祖先平衡因子**.

   > 不可能是2或-2变成1或-1,因为这是AVL树的插入,至少先保证是AVL树才能插入

   ![image-20240821143019855](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240821143019855.png)

2. 若`parent->_bf == 2 || parent->_bf == -2` ,说明插入前的父结点所在子树一边高一边低,之后新结点恰好插入到了高的一边,导致不平衡,需要**做旋转操作,调整平衡**.

3. `parent->_bf == 0`,插入后父结点的平衡因子平衡,说明原先父结点的左右子树是一边高一边低,然后插入刚好插到了低的一边,使其平衡.**插入结束**.



#### 旋转操作

##### 分析需要旋转的情况

首先，要针对AVL子树，找出/抽象出可能发生旋转的情况。

一棵可能发生旋转的树至少高度差为1，即两个结点以上。（前提）

![image-20240821220751643](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240821220751643.png)

 							（可能会发生旋转的子树至少两个结点以上） 

​	

其中a,b,c是三棵AVL子树

- 当子树高度h==0时,即a、b、c都为空树

- 当子树高度h==1时,a、b、c都是叶子结点

- 当子树高度h==2时,a、b、c分别有三种情况

  ![image-20240821221330259](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240821221330259.png)

  此时这个AVL子树有3\*3\*3\=27种情况：a为x/y/z，b为x/y/z，c为x/y/z。

- 如此往下，还有更多的情况，但全部形状都可以用图中模型来代替。

以h==2为例，**只有**当b或c为z情况时，插入到b或c子树会影响到根结点（30），并使其发生旋转。
其他情况都无法使其发生旋转。因此，当前可以总结出2种需要旋转的情况：

1. c为z时，插到c中（左左）
2. b为z时，插到b中（左右）

> 左左：较高的子树是左孩子（60）所在子树，插到左孩子（60）的左子树上（c）引发根（30）旋转的情况叫“左左”。
>
> 顺口：插在较高左子树的左孩子上。

同理，水平镜像翻转的AVL子树也同理

![image-20240822130346774](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240822130346774.png)

1. c为z时，插到c中（右右）
2. b为z时，插到b中（右左）

###### 结论

合并起来**总共4种需要旋转的情况**，验证其他高度也同样如此。



> 其中插入b子树使30结点发生旋转的情况：a为x/y/z，b为z，c为x/y/z，总共3\*3\=9种
>
> 其中插入c子树使30结点发生旋转的情况：a为x/y/z，b为x/y/z，c为z，总共3\*3\=9种
>
> 特例的数量非常多，无法穷举。





##### 4种旋转操方法与特征

1. 新节点插入较高左子树的左侧---左左：右单旋

   - 特征

     父：-2

     子：-1

   

   ![image-20240821222950717](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240821222950717.png)

   最左边高，旧根的左孩子变成新根，旧根成为新根的右孩子，同时领养新根的旧右孩子。

   > 儿子上位 -- 儿子当根
   >
   > 右单旋（主角是儿子）：老爹在我的右上方，让老爹以我为轴，旋转到我的右下方

2. 新节点插入较高右子树的右侧---右右：左单旋

   - 特征

     父：2

     子：1

   ![image-20240821223005559](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240821223005559.png)

   最右边高，旧根的右孩子变成新根，旧根成为新根的左孩子，同时领养新根的旧左孩子。

3. 新节点插入较高左子树的右侧---左右：先左单旋再右单旋

   - 特征

     父：-2

     子：1

   ![image-20240821223029508](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240821223029508.png)

   1. 旧根的左儿子的右孩子（简称右孙子）高：让右孙子成为旧根的左孩子，旧左孩子变成孙子的左孩子，同时领养孙子的左孩子。 -- 对右孙子做左旋操作
   2. 右孙子成为旧根的新左儿子，再对新作儿子做右旋操作即可。

   > 孙子上位 --- 孙子当根
   >
   > 感性描述：先左单旋再右单旋（孙子是主角）：我在孙子左边，我的老爹在孙子右边，然后让孙子的爹（我）左旋下来，孙子成为我的爹，我的旧爹成为孙子的爹；最后再让孙子的新爹右旋下来。
   >
   > 描述2： 两次旋转分别用途： 1. 转化成标准单旋； 2.标准单旋



4. 新节点插入较高右子树的左侧---右左：先右单旋再左单旋

   - 特征

     父：2

     子：-1

   ![image-20240821223118168](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240821223118168.png)



总共4种旋转的情况：

1. 右旋（左左）
2. 左旋（右右)
3. 先左旋再右旋（左右）
4. 先右旋再左旋（右左）

简要图：

![image-20240821222437676](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240821222437676.png)



##### 6种双旋平衡因子特征

容易发现单旋平衡因子都是0（高度差为0），而双旋平衡因子较为复杂，观察规律总结出一共6种情况。

1. 左右左（h>0)

   - 旧（特征）

     孙：-1

   - 新

     父：1

     子：0

     孙：0

   ![image-20240822210321326](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240822210321326.png)



2. 左右右(h>0)

   - 旧（特征）

     孙：1

   - 新

     父：0

     子：-1

     孙：0

   ![image-20240822211755508](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240822211755508.png)

> 



3. 右左右（h>0）

   - 旧（特征）

     孙：1

   - 新

     父：-1

     子：0

     孙：0

   

   ![image-20240822213701922](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240822213701922.png)

4. 右左左（h>0）

   - 旧（特征）

     孙：-1

   - 新

     父：0

     子：1

     孙：0

   ![image-20240822214246457](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240822214246457.png)



5. 左右，特例（h==0)

   - 旧（特征）

     孙：0

   - 新

     父：0

     子：0

     孙：0

   ![image-20240822212626720](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240822212626720.png)

6. 右左（h==0)，与5相同

   - 旧（特征）

     孙：0

   - 新

     父：0

     子：0

     孙：0

#### 代码实现

##### 四种旋转实现

```
 //1. 右右
    void RotateL(Node* parent) {
        //. 记录爷爷(父亲的父亲)
        //. 我是父的右儿子(我是主角)
        //. 记录下我的左子树(托管)
        //  旋转(爷、父、子关系重新调整）
        //      成为爷爷的右儿子 (如果没有爷爷,则跳过;且说明父是根,更新我成为根)
        //      把我的左子树托管给父成为他的右孩子
        //      旧父成为我的左儿子,旧父的父更新成我
        //. 更新平衡因子
              
        //. 记录爷爷(父亲的父亲)
        //. 我是父的右儿子
        //. 记录下我的左子树
        Node* pparent = parent->_parent;
        Node* cur = parent->_right;
        Node* leftchild = cur->_left;

        //旋转
        //. 成为爷爷的右儿子 (如果没有爷爷,则跳过;且说明父是根,更新我成为根)
        if (pparent) {              //有爷爷
            if(parent == pparent->_left)
                pparent->_left = cur;
            else {
                pparent->_right = cur;
            }
            cur->_parent = pparent; //三叉链维护
        }
        else {                      //没有爷爷,父亲是根
            cur->_parent = nullptr;
            _root = cur;
        }
        //. 父子地位交换
        parent->_right = leftchild;
        if (leftchild) {            //三叉链维护
            leftchild->_parent = parent;
        }
        cur->_left = parent;
        parent->_parent = cur;
        //旋转 【end】

        //更新平衡因子
        cur->_bf = 0;
        parent->_bf = 0;
    }


//2. 左左
    void RotateR(Node* parent) {
        //. 记录爷爷
        //. 我是父的左儿子
        //. 记录下我的右子树
        Node* pparent = parent->_parent;
        Node* cur = parent->_left;
        Node* rightChild = cur->_right;

        //旋转
        //. 成为爷爷的左儿子 (如果没有爷爷,则跳过;且说明父是根,更新我成为根)
        if (pparent) {              //有爷爷
            if (parent == pparent->_left)
                pparent->_left = cur;
            else {
                pparent->_right = cur;
            }
            cur->_parent = pparent; //三叉链维护
        }
        else {                      //没有爷爷,父亲是根
            cur->_parent = nullptr;
            _root = cur;
        }
        //. 父子地位交换
        parent->_left = rightChild;
        if (rightChild) {            //三叉链维护
            rightChild->_parent = parent;
        }
        cur->_right = parent;
        parent->_parent = cur;
        //旋转 【end】

        //更新平衡因子
        cur->_bf = 0;
        parent->_bf = 0;

    }
//3. 左右
    void RotateLR(Node* parent) {
        //我是儿子,但是主角是孙子
        //记录下孙子
        //记录下孙子的平衡因子(特征)
        //对孙子进行左单旋,再右旋
        //更新平衡因子
        Node* cur = parent->_left;
        Node* grandson = cur->_right;
        int bf = grandson->_bf;

        RotateL(cur);
        RotateR(grandson->_parent);

        //三种情况
        if (bf == 0) {
            parent->_bf = 0;
            cur->_bf = 0;
            grandson->_bf = 0;
        }
        else if (bf == 1) {
            parent->_bf = 0;
            cur->_bf = -1;
            grandson->_bf = 0;
        }
        else if (bf == -1) {
            parent->_bf = 1;
            cur->_bf = 0;
            grandson->_bf = 0;
        }
        else {
            assert(false); //错误检查
        }
    }

//4. 右左
    void RotateRL(Node* parent) {
        //我是儿子(父的右孩子),但是主角是孙子
        //记录下孙子(我的左孩子)
        //记录下孙子的平衡因子(特征)
        //对孙子进行右单旋,再左单旋
        //更新平衡因子
        Node* cur = parent->_right;
        Node* grandson = cur->_left;
        int bf = grandson->_bf;

        RotateR(cur); //将孙子的爹,就是我,进行右单旋
        RotateL(grandson->_parent); //将儿子的新爹进行左单旋

        //三种情况
        if (bf == 0) {
            parent->_bf = 0;
            cur->_bf = 0;
            grandson->_bf = 0;
        }
        else if (bf == 1) {
            parent->_bf = -1;
            cur->_bf = 0;
            grandson->_bf = 0;
        }
        else if (bf == -1) {
            parent->_bf = 0;
            cur->_bf = 1;
            grandson->_bf = 0;
        }
        else {
            assert(false);
        }
    }
```



##### 插入操作实现

```
    bool Insert(const std::pair<K,V> kv) {
        //第一个结点做根
        if (_root == nullptr) {
            _root = new Node(kv);
            _size++;
            return true;
        }

        //搜索
        Node* parent = _root;
        Node* cur = _root;
        while (cur) {
            //大于往右走
            if (kv.first > cur->_kv.first) {
                parent = cur;
                cur = cur->_right;
            }
            //小于往左走
            else if (kv.first < cur->_kv.first) {
                parent = cur;
                cur = cur->_left;
            }
            //找到了,存在相同的key
            else {
                return false;
            }
        } //循环搜索...

        //不存在,可以插入
        cur = new Node(kv);                         //new后,cur值发生改变,之后都不能使用地址进行比较
        if (cur->_kv.first < parent->_kv.first) { 
            parent->_left = cur;
        }
        else {
            parent->_right = cur;
        }
        cur->_parent = parent; //三叉链链上父结点
        _size++;

        //调整平衡因子 : 最多到根,根的parent为nullptr
        while (parent) {

            //更新平衡因子
            if (cur->_kv.first < parent->_kv.first) {
                parent->_bf--;
            }
            else {
                parent->_bf++;
            }

            //看是否需要调整
            if (parent->_bf == 1 || parent->_bf == -1) {
                cur = parent;
                parent = parent->_parent;
            }
            else if(parent->_bf == 0){
                break; 
            }
            else if(parent->_bf == 2 || parent->_bf == -2){
                if (parent->_bf == -2 && cur->_bf == -1) {      //左左
                    RotateR(parent);
                }
                else if (parent->_bf == 2 && cur->_bf == 1) {   //右右
                    RotateL(parent);
                }
                else if (parent->_bf == -2 && cur->_bf == 1) {  //左右
                    RotateLR(parent);
                }
                else if(parent->_bf == 2 && cur->_bf == -1){    //右左
                    RotateRL(parent);
                }
                else {                                          //错误检查
                    assert(false);
                }
                break;
            }
            else {
                assert(false);
            }
        }

        return true;
    }
```



##### 树高度与是否平衡树判断实现

```
    size_t Hight() {
        return _Hight(_root);
    }

    bool IsBalance() {
        return _IsBalance(_root);
    }
    
    size_t _Hight(Node* root) {
        if (root == 0) return 0;                //空
        size_t leftH = _Hight(root->_left);
        size_t rightH = _Hight(root->_right);
        return std::max(leftH, rightH) + 1;     //+1:自己高度为1
    }

    bool _IsBalance(Node* root) {
        if (root == nullptr) return true;
        int leftH = _Hight(root->_left);
        int rightH = _Hight(root->_right);
        int bf = rightH-leftH;
        return  bf == root->_bf         //平衡因子
            && (bf > -2 && bf < 2)      //高度差
            && _IsBalance(root->_left)  
            && _IsBalance(root->_right);
    }
```



##### 其他实现

```
#include<iostream>
#include<string>
#include<cassert>

template<class K,class V>
struct AVLTreeNode {
    
    //三叉链
    AVLTreeNode<K,V>* _left;
    AVLTreeNode* _right;
    AVLTreeNode* _parent;

    int _bf; //balance factor
    std::pair<K,V> _kv;

    AVLTreeNode(const std::pair<K,V>& kv)
        :_left(nullptr),
        _right(nullptr),
        _parent(nullptr),
        _bf(0),
        _kv(kv)
    {}
};

template<class K,class V>
class AVLTree {
public:
    using Node = AVLTreeNode<K, V>;
    AVLTree()
    :_root(nullptr)
    ,_size(0)
    {}

public:
    void InOrder() {
        _InOrder(_root);
        std::cout<<std::endl;
    }



private:
    void _InOrder(Node* root) {
        if (root == nullptr) {
            return ;
        }
        _InOrder(root->_left);
        std::cout<<root->_kv.first<<" ";
        _InOrder(root->_right);
    }



private:
        Node* _root;
        size_t _size;
};
```



#### 插入验证

1. 两个数组包含各种旋转情况
2. 每插入都判断是否平衡

```
int main() {
    std::cout<<std::boolalpha;
    //int a[] = { 4, 2, 6, 1, 3, 5, 15, 7, 16,14 };
    int a[] = { 16, 3, 7, 11, 9, 26, 18, 14, 15 };
    AVLTree<int, int> t;
    for (int it : a) {
        t.Insert(std::make_pair(it, it));
        std::cout << "是否平衡: " << t.IsBalance() << std::endl;
    }
    
    t.InOrder();								//3 7 9 11 14 15 16 18 26
}
```

![image-20240823115901985](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240823115901985.png)



#### BenchMark

##### 环境

| 架构：          | x86_64                            |
| --------------- | --------------------------------- |
| CPU 运行模式：  | 32-bit, 64-bit                    |
| CPU:            | 16                                |
| 在线 CPU 列表： | 0-15                              |
| 型号名称：      | AMD Ryzen 7 7840HS w/ Radeon 780M |
| CPU MHz：       | 3792.879                          |
| L1d 缓存：      | 512 KiB                           |
| L1i 缓存：      | 512 KiB                           |
| L2 缓存：       | 16 MiB                            |
| L3 缓存：       | 256 MiB                           |
| 系统：          | Win10                             |
| IDE：           | VS2019                            |



##### 测试工具和方法

工具

- void RandomArray_Generator(int* a, int n)：随机数生成器
- void Cost(std::function<void(void)> func)：计算函数执行时间花销。使用包装器接收任意可调用对象

测试方法

​	计算1000000个随机数，有序数，逆序数，重复数插入的时间开销。

```
void RandomArray_Generator(int* a, int n) {
    std::random_device rnd;//random num device //效率低，只用于生成种子
    std::mt19937 rng(rnd()); //random num generator -- 生成随机数
    std::uniform_int_distribution<int> uni(0, 1000000000);//整型区间筛选
    //[0-N]有6成为不重复,4成重复 --若需要9成不重复需要扩大筛选范围为10倍的N,即插入N需筛选10N

    //int a[] = { 3,1,8,4,2,7,5,9,6,0 }; //自定义数组
    int size = n;
    for (int i = 0; i < size; i++) {
        a[i] = uni(rng); //随机数
        //a[i] = size - i; //逆序
        //a[i] = i;         //正序
        //a[i] = size/2;     //重复数
        if (i % 10000 == 0) {
            a[i] = uni(rng);  //插入一些随机数
        }
    }
}

void Cost(std::function<void(void)> func) {
    auto begin = std::chrono::high_resolution_clock::now();
    func();
    auto end = std::chrono::high_resolution_clock::now();
    std::chrono::duration<double> cost = end - begin;
    std::cout<<cost.count()<<"/s" << std::endl;
}

void InsertTest(AVLTree<int,int>& t, int* a, int size) {
    for (int i = 0; i < size; i++) {
        t.Insert(std::make_pair(a[i], a[i]));
        //if (t.IsBalance() == false) assert(false);
    }
}


int main() {
     //int a[] = { 16, 3, 7, 11, 9, 26, 18, 14, 15 };
    //int a[] = { 4, 2, 6, 1, 3, 5, 15, 7, 16,14 };
    int size = 1000000;
    int* a = new int[size];
    RandomArray_Generator(a,size);
    AVLTree<int, int> t;
    InsertTest(t,a,size);
   Cost([&](){std::cout<<"cost: ";InsertTest(t, a, size); });
    //t.InOrder();
    std::cout<<std::boolalpha;
    std::cout << "是否平衡: " << t.IsBalance() << std::endl;
}
```

##### 测试结果：

- 随机数

  ![image-20240823161836694](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240823161836694.png)

- 逆序数

  ![image-20240823161903646](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240823161903646.png)

- 正序数

  ![image-20240823161927244](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240823161927244.png)

- 重复数

  ![image-20240823161956829](STL%20%E5%B9%B3%E8%A1%A1%E6%90%9C%E7%B4%A2%E6%A0%91-AVL%E6%A0%91%20C++%E5%AE%9E%E7%8E%B0.assets/image-20240823161956829.png)