---
title: leetcode入门题目第一弹
date: 2020-10-18 11:30:29
categories: leetcode
tags: leetcode
---
# leetdoe27移除元素
## 原地移除元素

给定一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

## 双指针的典型应用
```
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int j =0;
        for(int i = 0;i<nums.size();i++){
           if(nums[i] != val){
               nums[j] =nums[i];
               j++;
           } 
        }
        return j;
    }
};
```

## vector 删除元素

.clear()删除全部元素

.pop_back()删除末尾元素

.erase()删除指定位置元素，只输入一个int型数i则会删除i到末尾的全部元素

remove() 算法由定义在 algorithm 头文件中的模板生成，它可以删除匹配特定值的一段元素。例如：
```
std::vector<std::string> words { "one", "none","some", "all”, "none", "most","many"};
auto iter = std::remove(std::begin(words), std::end(words), "none");
```
第二条语句在头两个参数指定的元素范围内，移除了所有匹配 remove() 的第三个参数 string("none") 的元素。移除元素这个表述有一点误导，remove() 是一个全局函数，所以它不能删除容器中的元素。remove() 移除元素的方式和从字符串中移除空格的方式相似，都是通过用匹配元素右边的元素来覆盖匹配元素的方式移除元素。图 1 展示了这个过程：


图 1 remove() 算法的工作原理

如果在 remove() 操作后输出 words 中的元素，只会输出前 5 个元素。尽管 size() 返回的值仍然是 7，而且最后两个元素仍然存在，但是它们被替换成了空字符串对象。为了摆脱这些多余的元素，可以使用成员函数 erase()。remove() 返回的迭代器可以这样使用：
```
words.erase(iter, std::end(words));//Remove surplus elements
```
这被叫作 erase-remove，执行删除操作后，iter 指向最后一个元素之后的位置，所以它标识了被删除序列的第一个元素，被删除序列的结束位置由 std::end(words) 指定。当然，在一条语句中，也能先移除元素，然后再删除末尾不想要的元素：
```
words.erase(std::remove(std::begin(words), std::end(words),"none"), std::end(words));
```
remove() 算法返回的迭代器作为 erase() 的第一个参数，erase() 的第二个参数是所指向容器中最后一个元素后一个位置的迭代器。了解如何为 vector 容器分配额外容量，可以让你明白会产生多少额外开销，以及可分配的内存量。

# leetcode42 接雨水栈
## 数组内维护一个栈

使用栈来存储条形块的索引下标。
遍历数组：


当栈非空且当前遍历到的数组的高度大于栈顶的高度，

则可以界定中间的高度了弹出栈顶元素，计算距离，
C++



```
int trap(vector<int>& height)
{
    int ans = 0, current = 0;
    stack<int> st;
    while (current < height.size()) {
        while (!st.empty() && height[current] > height[st.top()]) {
            int top = st.top();//保存当前栈中最小高度位置的下标
            st.pop();
            if (st.empty())
                break;
            int distance = current - st.top() - 1;
            int bounded_height = min(height[current], height[st.top()]) - height[top];//可以看做是一层一层的叠加，bounded_height看做是左右中最小高度与前一个次小高度的高度差
            ans += distance * bounded_height;
        }
        st.push(current++);
    }
    return ans;
}
```
### cb快捷键
crtl + D 快速复制一行
### eclipse

ctrl +alt+下箭头
### 为什么不能在int数组初始化时直接赋值

 int count[26] ={101}

 这样的语句的最终结果是只有第一项是101，其余各项还都
 
 是直接赋值为零
 
# leetcode104 二叉树的最大深度  
## 递归、栈循环实现深度优先遍历
```
//深度优先：递归版
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(root==NULL) return 0;
        int l=maxDepth(root->left)+1;
        int r=maxDepth(root->right)+1;
        return l>r?l:r;   
    }
};
//深度优先：用栈的循环版
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(root==NULL) return 0;
        stack<pair<TreeNode*,int>> s;
        TreeNode* p=root;
        int Maxdeep=0;
        int deep=0;
        while(!s.empty()||p!=NULL)//若栈非空，则说明还有一些节点的右子树尚未探索；若p非空，意味着还有一些节点的左子树尚未探索
        {
            while(p!=NULL)//优先往左边走
            {
                s.push(pair<TreeNode*,int>(p,++deep));
                p=p->left;
            }
            p=s.top().first;//若左边无路，就预备右拐。右拐之前，记录右拐点的基本信息
            deep=s.top().second;
            if(Maxdeep<deep) Maxdeep=deep;//预备右拐时，比较当前节点深度和之前存储的最大深度
            s.pop();//将右拐点出栈；此时栈顶为右拐点的前一个结点。在右拐点的右子树全被遍历完后，会预备在这个节点右拐
            p=p->right;
        }
        return Maxdeep;
    }
};
//广度优先：使用队列
class Solution {
public:
    int maxDepth(TreeNode* root) {
         if(root==NULL) return 0;
         deque<TreeNode*> q;
         q.push_back(root);
         int deep=0;
         while(!q.empty())
         {
             deep++;
             int num=q.size();
             for(int i=1;i<=num;i++)
             {
                TreeNode* p=q.front();
                q.pop_front();
                if(p->left) q.push_back(p->left);
                if(p->right) q.push_back(p->right);
             }
         }
         return deep;         
    }
};


```
## 我的递归实现超时代码
```
    int dfs(int depth,TreeNode * node){
        //int l =depth,r = depth;
        if(!node)
            return depth;
        if(node->left){
            //l +=1;
           dfs(1+depth,node->left);
            
        }
           if(node->right){
            //r +=1;
               //node = node->right;
               dfs(1+depth,node->right);
        }
        return max(dfs(1+depth,node->left),dfs(1+depth,node->right));
    }
```

# leetcode107 二叉树的层次遍历
##  题目介绍
给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）
## 我的代码
```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int> > result;//
        vector<int> floor;
     TreeNode * q[10000];
        int front ,rear;
        TreeNode * p = root;
        front = rear = -1;
        int last = 0;
        if(!root) return result;
        q[++rear] = root;//first root put in queue
        while(front!=rear){
            p = q[++front];
      
            floor.push_back(p->val);
            if(p->left){
                q[++rear] = p->left;
            }
              if(p->right){
                q[++rear] = p->right;
            }
              if(front == last){
            last = rear;
            result.push_back(floor);
                  floor.clear();
        }
        }
    
        reverse(result.begin(),result.end());
        return result;
    }
};
```
### 利用队列实现，然后进行顺序的翻转
### stack 
思路：此题需要熟悉二叉树的层次遍历，对于常见的层次遍历是利用队列（先进先出）的特性进行实现的，总的思路就是我们每遍历一个节点就把该节点的左孩子和右孩子放进队列（顺序很重要），然后遍历完这个节点之后就从队列中取出头节点（取出的节点有可能是右边的同层节点，也有可能是下一层的第一个节点），然后直到遍历完成。以上就是层次遍历的思路。
对于本题，额外需要注意的是判断什么时候遍历完一层，我们可以采取使用一个记录下一层位置的一个值来判断什么时候完成遍历的。我们首先就应该在循环外面把根节点放入队列，然后令last等于0，这其实是让last指向队尾，什么时候我们从队列中取出元素，如果取出到last的时候就代表我们已经遍历完一层，同时每遍历完一层，就应该更新last的值，让last=rear,因为遍历完一层了，说明旗下所有的孩子均已经入队了，所以此时rear就对应出下一层的最大值。此算法强烈推荐大家记住，面试常考。

与老师进行沟通，根据教学计划制定相应的助教工作方案，协助老师整理算法题库和教辅资料，自己也进行相关算法的学习与复习，向老师了解本科生的学习情况，便于后续对学生平时的表现给予公正的评价。
# leetcode111 二叉树的最小深度
## 递归最终要的是先要缕清递归的结束条件
## 最难的是有有一颗子树不存在的情况利用右子树进行递归左子树同理
```
# java版本
class Solution {
    public int minDepth(TreeNode root) {
        if(root == null) return 0;
        int m1 = minDepth(root.left);
        int m2 = minDepth(root.right);
        //1.如果左孩子和右孩子有为空的情况，直接返回m1+m2+1
        //2.如果都不为空，返回较小深度+1
        return root.left == null || root.right == null ? m1 + m2 + 1 : Math.min(m1,m2) + 1;
    }
}


public:
    int minDepth(TreeNode* root) {
        //int min = 0;
        int l,r;
        if(!root)
            return 0;
       // int level =1;
        if(root->left==NULL&&root->right==NULL)
            return 1;//
        
        l = minDepth(root->left);
         r = minDepth(root->right);
         if((!root->right)||(!root->left))
             return l+r+1;
             
           
        
        
        return min(l,r)+1;
    }
};
```
# leetcode342 回文链表
## 翻转前半部分判断是否相等
思路：此题基本思路就是讲链表分为两段，并且把前面一段进行倒置，然后再比较前后两段是否相同即可，最简单的方法就是先计算链表的长度，然后将前面一半用头插法的办法进行倒置，最后再比较，但是由于需要的是一半这个特殊的位置，所以我们可以考虑快慢指针，何谓快慢指针，其实就是两个指针，一根指针一次移动一个位置，另一个指针一次移动两个位置，就好比两个人走路一个速度为v,一个速度为2v,当速度快的人走到底了设长度为x,这个速度快的人所话的时间就为x/(2v)而对于相同的时间而言对于那个走得慢的就走到的距离为d. d = v* t,而t为x/(2v),代入就为d = x/2所以当一个人走到底了另一个人正好走到了一半。所以快慢指针适合用于有中点相关的场景，同时为了更加简便，我们可以采用一边移动一边将其倒置，这个倒置的顺序只需要在图上画一画就凑出来了，同时需要注意链表个数为奇数和为偶数对于后一半的头结点位置有所不同。所以此题最关键就是可以在找到中点的同时就进行倒置了，也要记住有中点或倍数的时候可以考虑快慢指针来解决问题。
## 链表翻转的关键代码
```
ListNode * pre;
ListNode* s =NULL;
slow = head;
pre = slow;
slow = slow->next;
pre->next = s;
s = pre;
```
## mycode
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        if(!head||!head->next)
            return true;
        ListNode * slow,*fast;
        slow = fast = head;
        ListNode * s =NULL;
        ListNode * pre = NULL ;
        while(fast!=NULL&&fast->next!=NULL){//很关键的一个步骤能够便于后续区分奇数还是偶数，奇数时不是尾指针，偶数时则是尾指针,指向空指针,因为fast是一次跳两个因此要同时判断此时的fast和下一时刻的fast，不然会造成越界溢出
            pre =slow;
            slow = slow->next;
            fast = fast->next->next;
            pre->next = s;
            s = pre;
        }
        ListNode * tmp;
        tmp = slow;
        if(fast!=NULL) //判断出数目为偶数
        tmp =tmp->next;//偶数个后半部分则从中点n+1开始
        while(tmp!=NULL){
            if(tmp->val!=pre->val)
                return false;
            else{
                tmp = tmp->next;
                pre = pre->next;
            }
        }
        return true;
        
    }
};
```
# leetcode237 链表删除节点
## 如何在不知道前一个节点时来删除这个节点

先把后面的节点转存到要删除的节点然后删除后面的节点即可，然后删除后面的节点
# leetcode257 二叉树的所有路径
## erase（）
ector.erase()的功能从指定容器中删除指定位置的元素或某段范围内的元素，有以下两种重载方式：

iterator erase( iterator _Where);//删除指定位置的元素，返回值是一个迭代器，指向删除元素的下一个元素；
iterator erase( iterator _First, iterator _Last);//删除从_First开始到_Last位置(不包括_Last位置)的元素，返回值也是一个迭代器，指向最后一个删除元素的下一个位置。

调用erase()方法后，vector后面的元素会向前移位,一般在调用该方法后将迭代器自减一。


veci.erase(iter)之后，iter就变成了一个野指针，对一个野指针进行 iter++ 是肯定会出错
## dfs 实现利用传入参数的方式实现
 ```   vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> res;
        string path;
       dfs(root,path,res);
        return res;

    }
    void dfs(TreeNode * root,string path,vector <string> &res){
       if(!root) return;
        path.append(to_string(root->val));
        path.append("->");
        if(root->left==NULL&&root->right==NULL)
            path.erase(path.size()-1);
        res.push_back(path);
        dfs(root->left,path,res);
        dfs(root->right,path,res);
            
            
    }
```
### 用到的几个技巧
1 利用参数传递来进行每次的值得录入

2 string类型每次会创建一个新的副本

3 $引用可以对全局变量的值进行一个操作

4 string的最后一位默认是“”空值

## string 的 erase（）
1 string.erase(pos,n)          //删除从pos开始的n个字符    string.erase(0,1);   删除第一个字符

```
#include <string>
#include <iostream>
 
using namespace std;
 
int main()
{
   string::iterator i;
   string s;
   cin>>s;
   s.erase(1,2);
   cout<<s;
    return 0;
}
```
2   //删除pos处的一个字符（pos是string类型的迭代器）
```
string.erase(pos)      
#include <string>
#include <iostream>
 
using namespace std;
 
int main()
{
   string::iterator i;
   string s;
   cin>>s;
   i = s.begin()+3;
   s.erase(i);
   cout<<s;
    return 0;
}
```

3、string.erase(first,last)    //删除从first到last中间的字符（first和last都是string类型的迭代器）



```
#include <string>
#include <iostream>
 
using namespace std;
 
int main()
{
   string::iterator i;
   string s;
   cin>>s;
   s.erase(s.begin()+1,s.end()-1);
   cout<<s;
    return 0;
}
```
4 如果直接给一个int值，则表示从该int值处开始直到结尾
```  
cout << s.erase(s.size()-2)<<endl;
```


## STL的find，find_if函数提供了一种对数组、STL容器进行查找的方法。使用该函数，

需 #include <algorithm>
我们查找一个list中的数据，通常用find()，例如：
# leetcode349 两个数组的交集
## 利用set集合进行去重
set集合
set.insert（）函数

## Vector的assign（）函数
vector 有个函数assign, 可以帮助执行赋值操作。

assign会清空你的容器。
 

assign函数:

函数原型：

void assign(const_iterator first,const_iterator last);

void assign(size_type n,const T& x = T());

功能：

将区间[first,last)的元素赋值到当前的vector容器中，或者赋n个值为x的元素到vector容器中，这个容器会清除掉vector容器中以前的内容。
## my code
```
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        vector<int> nums;//可以直接set.insert()函数将元素插入到set集合中
        //set<int> res;
        int i ,j;
        i = j =0;
        if(nums2.size()==0||nums1.size()==0)
            return nums;
        sort(nums1.begin(),nums1.end());
        sort(nums2.begin(),nums2.end());//利用set去重
        while(i < nums1.size()&&j<nums2.size()){
            if(nums1[i]<nums2[j]){
                i++;
            }else if(nums1[i]>nums2[j]){
                j++;
            }else{
                nums.push_back(nums1[i]);
                i++;
                j++;
            }
            
        }
        set <int> s(nums.begin(),nums.end());
        nums.assign(s.begin(),s.end());
        return nums;
        
    }
};
```
# leetcode371 进位的两个整数之和
## 二进制异或可以进行无进位加法

我们知道，在位运算操作中，异或的一个重要特性是无进位加法

如果要得到 a + b 的最终值，我们还要找到进位的数，把这二者相加。在位运算中，我们可以使用与操作获得进位


利用位操作实现加法

首先看十进制是如何做的： 5+7=12，三步走

    第一步：相加各位的值，不算进位，得到2。
    第二步：计算进位值，得到10. 如果这一步的进位值为0，那么第一步得到的值就是最终结果。
    第三步：重复上述两步，只是相加的值变成上述两步的得到的结果2和10，得到12。

同样我们可以用三步走的方式计算二进制值相加： 5---101，7---111

    第一步：相加各位的值，不算进位，得到010，二进制每位相加就相当于各位做异或操作，101^111。
    第二步：计算进位值，得到1010，相当于各位进行与操作得到101，再向左移一位得到1010，(101&111)<<1。
    第三步重复上述两步，各位相加 010^1010=1000，进位值为100=(010 & 1010)<<1。
    继续重复上述两步：1000^100 = 1100，进位值为0，跳出循环，1100为最终结果。
    结束条件：进位为0，即a为最终的求和结果。

## 递归法求两数之和
```
 int getSum(int a, int b) {
        unsigned int c =a^b;
        unsigned int d =a&b;
         d = d<<1;
        while(d){
           
            return getSum(c,d);
        }
        return c;
    }
```

# leetcode387 字符串中的第一个唯一字符
## 利用map 超级简短明了的代码实现
```
class Solution {
  public:
    int firstUniqChar(string s) {
        unordered_map<char, int> map;
        for (auto &c : s) ++map[c];
        for (auto &c : s) if (map[c] == 1) return s.find_first_of(c);
        return -1;
    }
};
```

## 我的冗余没通过代码，本地能通过，leetcode报内存溢出
## 这种思路有问题只能解决配对问题，不能解决多个奇数的问题，例如三个重复
```
class Solution {
public:
    int firstUniqChar(string s) {
      vector<char> v;
        v.push_back(s[0]);
       // cout << v[i];
      int pos[26];
        for(int i = 0; i <26;i++){
            pos[i]  =-1;
        }
        pos[s[0]-97] = 0;
        for(int i = 1;i <s.size();i++){
            if(pos[s[i]-97]==-1){
                pos[s[i]-97] = i;
            }
            for(int j = 0; ;){
                if(s[i]==v[j]&&j<v.size()){//这里容易报堆栈溢出的错误应该先判断j是否与size（）相等了，如果相等则不进行下面的操作，v[j]不访问也就不会数组越界
                    cout << *(v.begin()+j);
                    v.erase(v.begin()+j);
                    break;

            }else if(s[i]!=v[j]&&j<v.size()){
                        j++;
                }else{
                       v.push_back(s[i]);
                break;
            }

        }
        }
        // cout << *(v.begin()+j);
          return pos[v[0]-97];
    }

};
```
# heap--overflow 大概率是数组越界等，因此要好好检查数组的使用情况


## 我的简便做法，两次遍历
```
if(s.size()==0)
            return -1;
        int pos[26] = {0};
        for(int i =0; i<s.size();i++){
            pos[s[i]-'a']++;
        }
        int i =0;
        for(; i<s.size();i++){
            if(pos[s[i]-'a']==1)
               return i;
        }
        
        return -1;
```
# leetcode389 找不同
## 位运算的应用
给定两个字符串 s 和 t，它们只包含小写字母。

字符串 t 由字符串 s 随机重排，然后在随机位置添加一个字母。

请找出在 t 中被添加的字母。

## 我的代码
```
 char findTheDifference(string s, string t) {
        char a = t[t.size()-1];
        for(int i = 0 ;i <s.size();i++){
            a^=s[i];
            a^=t[i];
        }
        return a;
    }
```
## 字符串替换法
```
public static char findTheDifference(String s, String t) {
    for(Character c : s.toCharArray()){
        t = t.replaceFirst(c.toString(),"");
    }
    return t.toCharArray()[0];
}


```
## map 计数法
```
public static char findTheDifference(String s, String t) {
    Character result = null;
    Map<Character, Integer> sMap = new HashMap<>();
    Map<Character, Integer> tMap = new HashMap<>();
    for (Character c : s.toCharArray()) {
        if (!sMap.containsKey(c)) {
            sMap.put(c, 0);
        }
        sMap.put(c, sMap.get(c) + 1);
    }

    for (Character c : t.toCharArray()) {
        if (!tMap.containsKey(c)) {
            tMap.put(c, 0);
        }
        tMap.put(c, tMap.get(c) + 1);
    }

    Set<Map.Entry<Character, Integer>> entries = tMap.entrySet();
    for (Map.Entry<Character, Integer> entry : entries) {
        Character key = entry.getKey();
        Integer value = entry.getValue();
        if (!value.equals(sMap.get(key))) {
            result = key;
            break;
        }
    }
    return result;
}
```

## ASCII 差值法
```
public static char findTheDifference(String s, String t) {
    int sCount = 0;
    int tCount = 0;
    for (Character c : s.toCharArray()) {
        sCount += c;
    }

    for (Character c : t.toCharArray()) {
        tCount += c;
    }

    return (char)(tCount - sCount);
}
```
## C++ vesrion:
t.toCharArray()
字符串的toCharArray（）方法
public char[] toCharArray()

java 语言中该方法的作用是返回一个字符数组，该字符数组中存放了当前字符串中的所有字符
 ```
public static char findTheDifference(String s, String t) {
    int sCount = 0;
    int tCount = 0;
    for (Character c : s.toCharArray()) {
        sCount += c;
    }

    for (Character c : t.toCharArray()) {
        tCount += c;
    }

    return (char)(tCount - sCount);
}
```
### C++中可以有这样的操作


```
char findTheDifference(string s, string t) {
        int sum = 0;
        for(int i=0;i<t.length();i++)
        {
            sum += t[i];
            if(i < s.length())
                sum -= s[i];
        }
        return (char)sum;
    }


 ```


# leetcode429 N叉树的层序遍历
## queue的应用
pop（）

front（）

push（）

back（）

## 问题的关键怎么判断节点在一层

入队列时先判断一下队列中的节点个数，在压入队列时一次性压入一层的节点，

在对队列的节点进行访问时，出队列前将其记录在vector中进行存储

## 代码如下：
```
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/
class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
        vector<vector <int> > res;
        queue <Node * > q;//怎么判断该层结束了？
        //一次压入一层
        q.push(root);
      //  vector<int> first;
        if(!root)
            return {};
       // first.push_back(root->val);
        //res.push_back(first);
        while(!q.empty()){
            int cnt = q.size();
           
            
            //q.pop();
              vector<int> ans;
            for(int i = 0;i<cnt;i++){
                Node * cur = q.front();
                q.pop();
                ans.push_back(cur->val);
                 for(auto n : cur->children){            
                      q.push(n);
                     
                } 
               /* if(n->val!=NULL){
                      ans.push_back(n->val);
            }
            */
                
            }
           if(ans.size()!=0)
            res.push_back(ans);
            
        }
        return res;
    }
};
```
# leetcode496 递增栈和递减栈
## 递增栈
1.元素入栈之后，其下面元素一定是其左边第一个比它大的数；(可用来求每个元素左边更大的第一个元素)
2.若在元素插入之前，栈顶元素比插入元素小，那么栈顶元素一定是插入元素左边第一个比它小的数
3.若在元素插入之前，栈顶元素比插入元素小，那么待插入元素是所有需要出栈的元素右边第一个更大的数； (可用来求每个元素右边更大的第一个元素)
4.最后一定会留下最大的数（对较大的数更有利）

```
/*1.正向用递增栈*/
/*class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        map<int,int> PMap;
        stack<int> UpStack;
        vector<int> result;
        for(int i=0;i<nums2.size();i++){
            if(UpStack.empty()||UpStack.top()>nums2[i])
                UpStack.push(nums2[i]);
            else{
                while(!UpStack.empty()&&UpStack.top()<nums2[i]){
                    PMap.insert({UpStack.top(),nums2[i]});
                    UpStack.pop();
                }
                UpStack.push(nums2[i]);
            }
        }
        while(!UpStack.empty()){
            PMap.insert({UpStack.top(),-1});
            UpStack.pop();
        }
        for(int i=0;i<nums1.size();i++){
            result.push_back(PMap[nums1[i]]);   //map的下标操作返回值是和value的类型一样的
        }
        return result;
    }
};*/

```
## 递减栈
1.元素入栈之后，其下面的元素一定是其左边第一个比它小的数；(可用来求每个元素左边更小的第一个元素)
2.若在元素插入之前，栈顶元素比插入元素更大，那么栈顶元素一定是待插入元素左边一个更大的数
3.若在元素插入之前，栈顶元素比插入元素更大，那么栈顶元素一定是所有需要出栈的元素右边更小的数；(可用来求每个元素右边更小的第一个元素)
4.最后一定会留下最小的数（对较小 的数更有利）
```
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        map<int,int> PMap;
        stack<int> DStack;
        vector<int> result;
        for(int i=nums2.size()-1;i>=0;i--){
            if(DStack.empty()){
                PMap.insert({nums2[i],-1});
                DStack.push(nums2[i]);
            }
            else if(DStack.top()>nums2[i]){
                PMap.insert({nums2[i],DStack.top()});
                DStack.push(nums2[i]);
            }
            else{
                while(!DStack.empty()&&DStack.top()<nums2[i])
                   DStack.pop();
                if(DStack.empty())
                   PMap.insert({nums2[i],-1});
                else
                   PMap.insert({nums2[i],DStack.top()}); 
                DStack.push(nums2[i]);
            }
        }
        for(int i=0;i<nums1.size();i++)
            result.push_back(PMap[nums1[i]]);   //map的下标操作返回值是和value的类型一样的
        return result;
    }
};
```

## map[[]]中的元素访问全都是用[]中括号来访问
# leetcode559 N叉树的深搜
## N叉树深搜利用for迭代器的循环
```
int maxDepth(Node* root) {
    if (!root) return 0;
    int m = 0;
    for (Node* it : root->children) 
        m = max(m, maxDepth(it));
    return ++m;
}
```

# leetcode682 棒球比赛
## 题目描述
你现在是棒球比赛记录员。
给定一个字符串列表，每个字符串可以是以下四种类型之一：
1.整数（一轮的得分）：直接表示您在本轮中获得的积分数。
2. "+"（一轮的得分）：表示本轮获得的得分是前两轮有效 回合得分的总和。
3. "D"（一轮的得分）：表示本轮获得的得分是前一轮有效 回合得分的两倍。
4. "C"（一个操作，这不是一个回合的分数）：表示您获得的最后一个有效 回合的分数是无效的，应该被移除。

每一轮的操作都是永久性的，可能会对前一轮和后一轮产生影响。
你需要返回你在所有回合中得分的总和。

## 怎么实现string类型向int型的转换

## string 和 int 的相互转换
1 to_string
包含在# include<string>。作用是把数值类型如int、double、long等转化为string
```
int a = 4;
double b = 3.14;
string str1, str2;
str1 = to_string(a);
str2 = to_string(b);
cout << str1 << endl;
cout << str2 << endl;
```
2 stoi和atoi
包含在#include<string>，不是c++11的可以在#include<cstring>。作用是将字符串转化为int型。区别是stoi的形参是const string*，而atoi的形参是const char*。c_str()的作用是将const string*转化为const char*
```
string s1("1234567");
char* s2 = "1234567";
int a = stoi(s1);
int b = atoi(s2);
int c = atoi(s1.c_str());
cout << a << endl;
cout << b << endl;
cout << c << endl;

```
## ps
codeblocks 存在bug不能很好支持to_string和stoi

因此需要替换MinGW中的头文件

将include文件夹下的wchar.h和stdio.h拷贝到MinGW的include文件夹中，一般是C:\mingw\include，如果你的codeblocks集成了MinGW则首先要从你的codeblocks安装目录中找到MinGW文件夹，拷贝到其下的include文件夹。
C:\mingw\include 或者 D:\Program Files\CodeBlocks\MinGW\include（记得找你自己的codeblocks安装目录）

将os_defines.h拷贝到MinGW安装目录的如下目录：
C:\mingw\lib\gcc\mingw32\4.7.0\include\c++\mingw32\bits
当然如果codeblocks集成MinGW，你要拷贝到对应的MinGW目录下对应的文件夹。

# leetcode739 每日气温
## 首先确定一下基础语法 for（）定义域
```
for(int i = 0 ;i<T.szie();i++){

}

cout<<i;


这样是会报错，找不到i的声明

```
## 想要后续继续使用i特别是i的结果值，那就要在for（）外面定义int i
```
int i;
for(i=0;i <T.size();i++){

}
```
## 问题描述
给定一个列表 temperatures = [73, 74, 75, 71, 69, 72, 76, 73]，你的输出应该是 [1, 1, 4, 2, 1, 1, 0, 0]。

提示：气温 列表长度的范围是 [1, 30000]。每个气温的值的均为华氏度，都是在 [30, 100] 范围内的整数。

## 我的不考虑效率解法，超时！
```
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        vector<int> result;
        
        for(int i = 0;i <T.size();i++){
        int j = i;
        bool flag = false;
        while(j<T.size()-1){
            j++;
            if(T[j]>T[i]){
                result.push_back(j-i);
                flag = true;
                break;
            }        
        }
        if(j==T.size()-1&&flag==false){
        result.push_back(0);
        }
        }
        return result;
    }
};

```

## 维护一个递减栈

class Solution {
public:
//维护递减栈，stack的元素都是递减的
    vector<int> dailyTemperatures(vector<int>& T) {//最后一天推到第一天，这样会简单很多。因为最后一天显然不会再有升高的可能，结果直接为0。
      stack <int> sta;
      
     // vector<int> res();不加参数会报错
      vector<int> res(T.size(),0);
     
      for(int i = 0;i<T.size();i++){
        while(!sta.empty()&&T[sta.top()]<T[i]){
        res[sta.top()]=(i-sta.top());
        sta.pop();
        }
        sta.push(i);
      }
    return res;
    } 

};

## 前置题目496和503

## 单调栈设计与模板
vector<int> nextGreaterElements(vector<int>& nums) {
    int n = nums.size();
    vector<int> res(n); // 存放结果
    stack<int> s;
    // 假装这个数组长度翻倍了
    for (int i = 2 * n - 1; i >= 0; i--) {
        while (!s.empty() && s.top() <= nums[i % n])
            s.pop();
        res[i % n] = s.empty() ? -1 : s.top();
        s.push(nums[i % n]);
    }
    return res;
}


# leetcode859 亲密字符串

## 最主要的易错点是容易漏掉判断条件致使出错
## mycode
```
class Solution {
public:
    bool buddyStrings(string A, string B) {
        if(A.size()!=B.size())
            return false;
        set <char> s;
          
        int i = 0;
        int flag =0;
        char tmp;
        while(i<A.size()){
            if(A[i]==B[i])
            {
                i++;
                  s.insert(A[i]);
            }else if(A[i] == B[A.size()-1-i]){
                flag++;
                 i++;
                s.insert(A[i]);
               
            }else{
                return false;
            }
        }
       // cout <<s.size()<A.size()<<flag;
        if(flag ==2||(flag==0&&s.size()<A.size())){
            return true;
        }
        return false;
    }
};
```
## code 技巧

括号一定要对齐，判断条件一定要对应，看看if之后的else条件是否都是满足的
## set 的应用
set主要是insert（）方法
## string 字符处理


## 怎么对字符串中的元素进行排序？


## leetcode1071字符串的最大公因子
## 回顾两个数的最大公因子
### 辗转相除法
```
onst gcd = (a, b) => (0 === b ? a : gcd(b, a % b))
```

```
  string gcdOfStrings(string str1, string str2) {
        //int a[str1.size()];
        int l1 = str1.length();
        int l2 = str2.length();
        if(l1==l2){
            if(str1==str2)
                return str1;
            else{
                return "";
            }
        }else if(l1>l2){
            string substr = str1.substr(l2);
            return gcdOfStrings(substr,str2);
        }else{
        string substr = str2.substr(l1);
        return gcdOfStrings(substr,str1);
    }
    }
```
## 上面的算法是在字符串按顺序排列的基础上，如果字符串不是按顺序的呢？

sort（string.begin(),string.end()）
sort()函数的功能实在是强大啊

substr（）函数能够对字符串进行切分是一个很好的工具
# leetcode897 递增顺序树

## 给定一个树，按中序遍历重新排列树，使树中最左边的结点现在是树的根，并且每个结点没有左子结点，只有一个右子结点。
##  中序遍历加重排
```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* increasingBST(TreeNode* root) {
        vector<int> tree;
        //构造新树
        //TreeNode * r1;
        midorder(root,tree);
    TreeNode  *  r1 = new TreeNode(0);
    TreeNode * cur = r1;
        //r1->val = tree[0];
        
        for(int i =0; i< tree.size();i++){
            cur->left = NULL;
            cur->right = new TreeNode(tree[i]);
            //cur = r1->right;这么简单的错误寻找了半天
           cur =  cur->right;
        }
        return r1->right;
    }
    
    void midorder(TreeNode * root,vector<int> &tree){
        if(!root) return;
        midorder(root->left,tree);
        tree.push_back(root->val);
        midorder(root->right,tree);
    }
    
};
```

# leetcode917 字符串翻转操作
## isalpha（）函数
原型：int isalpha（int ch）

判断字符ch是否为英文字母，当ch为英文字母a-z或A-Z时，返回非零值，否则返回零。


使用时需包含头文件<ctype.h>
## A的ASCII码是65,a的ASCII码是97。 

ASCII码表中,小写字母排在大写字母的后面,一个字母的大小写数值相差32

## string 的子字符串
string s2 = s1.substr(2, 4); 

## mycode

<ctype>里面还有很多string类型的操作
比如isalnum（）字母或数字

iscontrl（）控制字符

isdigit（）数字

islower（）小写

isspace（）空格

toupper（）转大写

tolower（）转小写
等等

```
class Solution {
public:
    string reverseOnlyLetters(string S) {
          if(S.size()==0)
            return S;
        string s1 (S);
        //s1 = S;
        int i = S.size()-1;
        int j =0;
      
        while (i>=0){
           if(('A'<=S[i])&&(S[i]<='Z')||('a'<=S[i])&&(S[i]<='z')){//不能将这种条件一起判断应该分两次判断
               
               if(('A'<=s1[j])&&(s1[j]<='Z')||('a'<=s1[j])&&(s1[j]<='z'))
               {
                   s1[j] = S[i]; 
                   i--;
                   j++;
               }else{
                   j++;
               }
                
           }
           else{
               i--;
           }
        }
        return s1;
    }
};
```

# leetcode938 二叉搜索树的范围和
## 二叉搜索树的范围和
给定二叉搜索树的根结点 root，返回 L 和 R（含）之间的所有结点的值的和。

二叉搜索树保证具有唯一的值。

 ## 解法利用递归
 题意：这个题字面含义很难理解，本意就是求出所有 X >= L 且 X <= R 的值的和
递归终止条件：

    当前节点为 null 时返回 0
    当前节点 X < L 时则返回右子树之和
    当前节点 X > R 时则返回左子树之和
    当前节点 X >= L 且 X <= R 时则返回：当前节点值 + 左子树之和 + 右子树之和

注意点：通过判断X的大小能够避免遍历全部树的节点，比如下方的动图中，3 这个值就没有必要遍历
```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int rangeSumBST(TreeNode root, int L, int R) {
        if (root == null) {
            return 0;
        }
        if (root.val < L) {
            return rangeSumBST(root.right, L, R);
        }
        if (root.val > R) {
            return rangeSumBST(root.left, L, R);
        }
        return root.val + rangeSumBST(root.left, L, R) + rangeSumBST(root.right, L, R);
    }
}
```
```
我的代码
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int rangeSumBST(TreeNode* root, int L, int R) {
        int ans =0;
        int l,r,v;
        l = r = 0;
        if(root==NULL){
            return 0;
        }
       if(root->val<L){
          ans = rangeSumBST(root->right,L,R); 
       } 
        else if(root->val>R){
            ans = rangeSumBST(root->left,L,R);
        }else {
            v = root->val;
            l = rangeSumBST(root->right,L,R);
            r = rangeSumBST(root->left,L,R);
            ans = v+l+r;
        }
       return  ans; 
    }
};
```
## 学会reverse（）函数的使用
string res

reverse(res.begin(),res.end());

# leetcode993 二叉树的堂兄弟节点
## 两种实现方式

一种是利用层序遍历，有点类似于线段树

第二种是直接带参数的dfs，之后进行判断

## 层序遍历解决

## 带参数的dfs


# leetcode997
## 图的应用
在一个小镇里，按从 1 到 N 标记了 N 个人。传言称，这些人中有一个是小镇上的秘密法官。

如果小镇的法官真的存在，那么：

    小镇的法官不相信任何人。
    每个人（除了小镇法官外）都信任小镇的法官。
    只有一个人同时满足属性 1 和属性 2 。

给定数组 trust，该数组由信任对 trust[i] = [a, b] 组成，表示标记为 a 的人信任标记为 b 的人。


## 利用vector来进行vector数组的截取
```
 TreeNode * root = new TreeNode(nums[mid]);
        vector<int> vec_left(nums.begin(),nums.begin()+mid);
        root->left = sortedArrayToBST(vec_left);
         vector<int> vec_right(nums.begin()+mid+1,nums.end());
```
# leetcode999
## 题目描述
在一个 8 x 8 的棋盘上，有一个白色车（rook）。也可能有空方块，白色的象（bishop）和黑色的卒（pawn）。它们分别以字符 “R”，“.”，“B” 和 “p” 给出。大写字符表示白棋，小写字符表示黑棋。

车按国际象棋中的规则移动：它选择四个基本方向中的一个（北，东，西和南），然后朝那个方向移动，直到它选择停止、到达棋盘的边缘或移动到同一方格来捕获该方格上颜色相反的卒。另外，车不能与其他友方（白色）象进入同一个方格。

## 我的最普通解法
### 超时
```
class Solution {
public:
    int numRookCaptures(vector<vector<char>>& board) {
        int count =0;
        int row,col;
        for(int i = 0; i < board.size();i++){
            for(int j = 0; j < board[0].size();j++){
                if(board[i][j] == 'R'){
                    row = i;
                    col = j;
                    break;
                }
            }
        }
        for(int i = 0;i < row;i++){
            if(board[i][col] == 'B'){
                int tmp = i;
                for(int m = 0;m<tmp;m--){
                    if(board[i][col] == 'p')
                        count--;
                }
            }else{
                if(board[i][col] == 'p'){
                    count++;
                }
            }
        }
        
        for(int i = row+1;i < board.size();i++){
            if(board[i][col] == 'B'){
                int tmp = i;
                for(int m = tmp+1;m<board.size();m++){
                    if(board[i][col] == 'p')
                        count--;
                }
            }else{
                if(board[i][col] == 'p'){
                    count++;
                }
            }
        }
        
        
         for(int j = 0;j < col;j++){
            if(board[row][j] == 'B'){
                int tmp = j;
                for(int m = 0;m<tmp;m--){
                    if(board[row][m] == 'p')
                        count--;
                }
            }else{
                if(board[row][j] == 'p'){
                    count++;
                }
            }
        }
        
        for(int j = col+1;j < board[1].size();j++){
            if(board[row][j] == 'B'){
                int tmp = j;
                for(int m = tmp+1;m<board.size();m++){
                    if(board[row][j] == 'p')
                        count--;
                }
            }else{
                if(board[row][j] == 'p'){
                    count++;
                }
            }
        }
        
        return count;
 
    }
};
```

#### // 不应该设置这么多重的循环
## 在一个循环里面利用flag，还有加深for的理解，可以用if和break来整理某些逻辑
## 先找到车的位置
**以车的位置上下左右四个点为起点按行按列搜索**

**碰到象挡路直接break；**

**否则搜索小卒的位置，搜索到则加一**

```
class Solution {
public:
    int numRookCaptures(vector<vector<char>>& board) {
        int count =0;
        int row,col;
        for(int i = 0; i < board.size();i++){
            for(int j = 0; j < board[0].size();j++){
                if(board[i][j] == 'R'){
                    row = i;
                    col = j;
                    break;
                }
            }
        }
        for(int i = row-1;i > 0;i--){

            if(board[i][col] == 'B')
                break;
            else if(board[i][col] =='p'){
                count++;
                break;
            }
        }
        
        for(int i = row+1;i < board.size();i++){

            if(board[i][col] == 'B')
                break;
            else if(board[i][col] == 'p'){
                count++;
                break;
            }
        }
        
        
         for(int j = col-1;j >=0;j--){

             if(board[row][j] == 'B')
                 break;
             else if(board[row][j] == 'p'){
                 count++;
                 break;
                 
             }
        }
        
        for(int j = col+1;j < board[1].size();j++){

              if(board[row][j] == 'B')
                 break;
             else if(board[row][j] == 'p'){
                 count++;
                 break;
                 
             }
        }
        
        return count;
 
    }
};
```

# leetcode1027 独一无二的数
## 题目描述
给你一个整数数组 arr，请你帮忙统计数组中每个数的出现次数。

如果每个数的出现次数都是独一无二的，就返回 true；否则返回 false。

## map 容器

map 容器中first 和second 可以用vector等任何其他的容器来进行定义
例如
map<int,int> count;
map <int, vector<int> > result;
## for循环的另一种表示形式
C++11中的新的特性
跟java中的for循环的两种形式比较类似，直接对对象进行for循环枚举，比平常int型的定义更加的方便特别是当size未知时这种

循环方式更加的便利和高效
### for(auto c : count)

```
class Solution {
public:
    bool uniqueOccurrences(vector<int>& arr) {
        map<int,int> count;//count the number of arry[i]        
        map<int,vector<int>> result;// vector storage the the number of arry[i]
        sort(arr.begin(),arr.end());
        for(int i =0; i< arr.size(); i++){
            count[arr[i]]++;
        }
        
        for(auto c : count){
            result[c.second].push_back(c.first);
        }
        for(auto r : result){
            if(r.second.size() > 1)
                return false;
        }
    return true;
    }
};
```
## 求气球的个数
给你一个字符串 text，你需要使用 text 中的字母来拼凑尽可能多的单词 "balloon"（气球）。

字符串 text 中的每个字母最多只能被使用一次。请你返回最多可以拼凑出多少个单词 "balloon"。
## 我的方法
```class Solution {
public:
    int maxNumberOfBalloons(string text) {//第一次没看题解，自己提交通过纪念一下
        map<int,int> ma;//
      //  map<int,vector<int> result;
        //int b,a,l,o,n;
        int ans = 0;
        map<char,int> ballon;
        for(int i = 0; i <text.size(); i++){
            ballon[text[i]]++;
        }
       /* for(int j = 0 ;j <ballon.size(); j++){
            if(ballon[j].first=='b'){
                b = ballon[i]
            }
        }
        */
        for(auto b: ballon){
            if(b.first == 'b'){
                ans =b.second;
            }
            if(b.first == 'a'){
                ans = min(ans,b.second);
            }
             if(b.first == 'n'){
                ans = min(ans,b.second);
            }
            if(b.first == 'l'){
              ans = min(ans,b.second/2);
            }
            if(b.first == 'o'){
                ans = min(ans,b.second/2);
            }
        }
        return ans;
    }
};
```
##  用switch 的方法来对进行有限的情况来进行枚举也不失为一种好方法
```
class Solution {
public:
int maxNumberOfBalloons(string text) {
int a1[5]={0,0,0,0,0};
for(int i=0;i<text.length();i++)
{
switch(text[i])
{ case 'b' : a1[0]+=1;
break;
case 'a' : a1[1]+=1;
break;
case 'l' : a1[2]+=1;
break;
case 'o' : a1[3]+=1;
break;
case 'n' : a1[4]+=1;
break;
default :
break;
}
}
a1[2]=a1[2]/2;
a1[3]=a1[3]/2;
int count=a1[0];
for(int i=1;i<5;i++)
{
if(a1[i]<count)
{
count=a1[i];
}
}
return count;
}
};
```



# leetcode1029 两地调度
公司计划面试 2N 人。第 i 人飞往 A 市的费用为 costs[i][0]，飞往 B 市的费用为 costs[i][1]。

返回将每个人都飞到某座城市的最低费用，要求每个城市都有 N 人抵达。


## map怎么进行排序?
第一反应是利用stl中提供的sort算法实现，这个想法是好

的，不幸的是，sort算法有个限制，利用sort算法只能对

序列容器进行排序，就是线性的（如vector，list，

deque）。map也是一个集合容器，它里面存储的元素是

pair，但是它不是线性存储的（前面提过，像红黑树），所

以利用sort不能直接和map结合进行排序。


我们知道map中的元素类型为pair，具体定义如下：

template <class T1, class T2> struct pair
{
  typedef T1 first_type;
  typedef T2 second_type;

  T1 first;
  T2 second;
  pair() : first(T1()), second(T2()) {}
  pair(const T1& x, const T2& y) : first(x), second(y) {}
  template <class U, class V>
    pair (const pair<U,V> &p) : first(p.first), second(p.second) { }
}


pair也是一个模板类，这样就实现了良好的通用性。它仅有两个数据成员first 和 second，即 key 和 value，而且

在 <utility>头文件中，还为pair重载了 < 运算符， 具体实现如下： 

```
template<class _T1, class _T2>
    inline bool
    operator<(const pair<_T1, _T2>& __x, const pair<_T1, _T2>& __y)
    { return __x.first < __y.first
             || (!(__y.first < __x.first) && __x.second < __y.second); }
```
## 思路一 将键和值进行对调进而按照键来进行访问，自动实现了排序
在实际的项目中可能会碰到key重复的情况，正常的MAP类型

是不允许重复的key，所以就要使用multimap了，

multimap的使用和map基本类似，可以无缝对接
## 但是两者的插入方式还是有区别的

//name_score_map["LiMin"] = 90;　　//主要插入的方式和map有所区别
name_score_map.insert(make_pair("LiMin", 90));
//name_score_map["LiMin"] = 91;
name_score_map.insert(make_pair("LiMin", 91));
//name_score_map["ZiLinMi"] = 79;
name_score_map.insert(make_pair("ZiLinMi", 79));
//name_score_map["BoB"] = 92;
## 思路二 利用pair中的value值进行比较，进而排序找出，前一半

```
static bool cmp(const pair<int,int>& a,const pair<int,int>& b)
    {
        return a.first<b.first;
    }

    int twoCitySchedCost(vector<vector<int>>& costs) {
        int n=costs.size();
        int n2=n/2;
        vector<pair<int,int>> cha(n);
        for(int i=0;i<n;++i)
        {
            cha[i]=make_pair(costs[i][1]-costs[i][0],i);
        }

        stable_sort(cha.begin(),cha.end(),cmp);

        int res=0;
        for(int i=0;i<n2;++i)
        {
            res+=costs[cha[i].second][1];
        }
        for(int i=n2;i<n;++i)
        {
            res+=costs[cha[i].second][0];
        }
        return res;
    }


```
利用vector进行比较，非常值得借鉴，构造一个sort的比较方案
##  错误解析

i 的作用域错误，致使半天没有发现错误，实际上思路是没有问题的，以后要特别注意避免犯这样的错误。
# leetcode1170 最小字母出现频次
## mycode
```
class Solution {
public:
    vector<int> numSmallerByFrequency(vector<string>& queries, vector<string>& words) {
        vector<int> res;
        int i;
        i =0;
        while(i<queries.size()){
            int count =0;
            for(int j=0;j<words.size();j++){
                if(f(queries[i])<f(words[j]))
                    count++;
            }
            res.push_back(count);
            i++;
        }
        return res;
    }
    int f(string s){
        int cnt =0;
        char min = s[0];
        for(int i = 1; i < s.size();i++){
            if(min>s[i])
                min = s[i];
        }
        for(int i = 0; i <s.size();i++){
            if(s[i] ==min)
                cnt++;
        }
        return cnt;
    }
};

```
## 改进 存储后来的值
```
class Solution {
public:
    int f(string& s) {
        char min = s[0];
        int count = 1;
        int length = s.length();
        for (int i = 1; i < length; i++) {
            if (s[i] < min) {
                min = s[i];
                count = 1;
                continue;
            }
            if (s[i] == min) {
                count++;
            }
        }
        return count;
    }
    
    vector<int> numSmallerByFrequency(vector<string>& queries, vector<string>& words) {
        vector<int> result;
        vector<int> fwords;
        //先计算好words数组中每个元素调用f函数的值，存到fwords数组中
        for (auto it = words.begin(); it != words.end(); it++) {
            fwords.push_back(f(*it));
        }
        sort(fwords.begin(), fwords.end());
        int size = fwords.size();
        for (auto it = queries.begin(); it != queries.end(); it++) {
            int fquery = f(*it);
            //对于每个f(queries[i])，用二分查找找到应该插入的位置，再用size减去位置，就得到了数组中比它大的元素个数
            result.push_back(size - (upper_bound(fwords.begin(), fwords.end(), fquery) - fwords.begin()));
        }
        return result;
    }
};


```
 