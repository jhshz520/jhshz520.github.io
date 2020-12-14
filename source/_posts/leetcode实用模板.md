---
title: leetcode实用模板
date: 2019-12-24 15:13:45
categories: Cpp算法实践
tags: leetcode
---
# 模板的重要性
很多时候刷完leetcode，一道题当时的印象比较深刻可是实践过得久了，再看到同样的题目，还是很难快速的反应过来

因此，总结一套自己的万能模板，能够达到事半功倍的效果，当然自己短期的一些训练与很早就从事算法竞赛的同学相比

有很大的差距，当时在锻炼自己的思维能力和编程方面还是能起到不小的作用的

## 关于博客的一些自己的看法
博客本来就是记述一下自己的学习过程，所以形式不是最重要的不要刻意地为了写博客而写博客应该是能够针对自己当时问题的解决方法有一个明确的解释提醒自己相同的问题可能的解决方案可能会从什么地方入手，就跟高中的错题本一样，不能本末倒置

## 回溯算法与剪枝

做搜索、回溯问题的套路是画图，代码其实就是根据画出的树形图写出来的。

根据题目中的用例，画一个图，因为是搜索，因此呈现的是一个树形结构图，并且在这个树形结构中会体现出递归结构。
根据题目中的用例，比对自己画图的结果和题目的结果的差异，如果一样，说明我们的分析没有错；如果不一样，说明我们的分析有误，一定有哪一个环节漏掉了或者分析错误，根据找到的问题调整算法。


for（）语句中第二个条件通常就是DFS的剪枝语句
class Solution {
    private:
    vector<vector<int>> res;
    vector<int> candidates;
    vector<int> path;

public:
    
        void DFS(int start, int target){
        if(target ==0){
            res.push_back(path);
            return;
        }
        for(int i = start;i<candidates.size()&&target-candidates[i]>=0;i++){
            path.push_back(candidates[i]);
            DFS(i,target-candidates[i]);
            path.pop_back();
        }
    }
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
      //取一个的情况，试一下是哪些元素的倍数
    //两个组合后，试一下是哪些元素组合的倍数
     //n的组合看是否是这些元素的倍数
     // 没看代码前我的思路
       // vector<int> path;
        //vector<vector<int>> res;
       //容易出错的步骤，如果不排序将不输出结果，而且必须是先进行排序进而赋值 
        this->candidates = candidates;
        sort(candidates.begin(),candidates.end());
        DFS(0,target);
       return res;
    }



};
## DFS 与图像渲染
```
class Solution {
public:
vector<vector<int> >image;
int newColor;
    void dfs(int sr,int sc,int color,vector<vector<int>> &image,int newColor){
        if(sr>=image.size()||sc>=image[0].size()||sr<0||sc<0)
        return;
        if(image[sr][sc]!=color)
        return;
        image[sr][sc] = newColor;
        dfs(sr+1,sc,color,image,newColor);   
         dfs(sr-1,sc,color,image,newColor);
        dfs(sr,sc-1,color,image,newColor);      
        dfs(sr,sc+1,color,image,newColor);
    }
    vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int newColor) {
        int color = image[sr][sc];
       // this->image = image;
       // this->newColor = newColor;
        if(color == newColor){
            return image;
        }
        dfs(sr,sc,color,image,newColor);
        return image;
    }
};
```
## 递归判断镜像二叉树
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
    bool ismirror(TreeNode * root1,TreeNode * root2){
        if(root1==NULL&&root2==NULL){           
           return true;
        }
        if(root1==NULL||root2==NULL){           
           return false;
        }
     return ((root1->val==root2->val)&&(ismirror(root1->left,root2->right))&&ismirror(root1->right,root2->left));
     
    }
    bool isSymmetric(TreeNode* root) {
     return ismirror(root,root);
    }
};
```

## 利用队列层次遍历二叉树
```
Q：队列;
root 入Q;
while(Q不空){
    cnt = Q.length;
    for(i in [0,...,cnt)){
        p = Q队首弹出;
        if(p的左子树) p的左子树入Q;
        if(p的右子树) p的右子树入Q;
    }
}


```
```
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {//层序遍历取最右的节点
    
    queue<TreeNode * > que;
    vector<int> res;
    if(!root)
    return res;//只要出现输入值是空的情况返回错误，都要增加初始值是否为空的判断及处理
        if(root);
        que.push(root);
        while(que.size()){
            int cnt = que.size();
            for(int i = 0; i< cnt; i++){
               TreeNode * cur = que.front();
                que.pop();
                if(i==cnt-1) res.push_back(cur->val);
                if(cur->left) que.push(cur->left);
                if(cur->right) que.push(cur->right);

            }
        }
        return res;
    }
};
```

##  并查集
```
class Solution {
    
public:
    int pre[100001];
    int find(int root){
        int son,tmp;
        son =root;
        while(root!=pre[root])
            root = pre[root];
        while(son!= root){
            tmp = pre[son];
            pre[son] = root;
            son =tmp;
        }
        return root;
    }
    string smallestStringWithSwaps(string s, vector<vector<int>>& pairs) {
        int size = s.size();
        for(int i =0;i<size;i++){
            pre[i] = i;
        }
        for(auto item:pairs){
            pre[find(item[0])] = find(item[1]);
        }

        vector<vector<char> >v(size);

        for(int i =0;i<size;i++){
            v[find(i)].push_back(s[i]);
        }

        string res;

        for(int i=0;i<size;i++){
            sort(v[i].rbegin(),v[i].rend());
        }

        for(int i =0;i<size;i++){
            res+=v[pre[i]].back();
            v[pre[i]].pop_back();
        }
        return res;
    }
};

```