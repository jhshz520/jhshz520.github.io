---
title: leetcode的playground模板
date: 2019-10-29 09:45:25
categories: 工具
tags: leetcode
---
# leetcode 是可以转移到本地进行调试的

将playground的代码复制到本地，然后加上各种头文件比如string
algorithm，sstream，using namespace等

# 本地文件的读写
引入cstdio头文件
main（）函数中加入
freopen（“in.txt","r",stdin）；

可以利用输入与输出的方式直接对自己想要调试的文件，在断点处添加一些输出的语句将自己想要观察的变量值进行输出
# 例子
```
#include <string>
#include<iostream>
#include <sstream>
#include<vector>
#include<queue>
#include<algorithm>
#include<cstdio>
using namespace std;

  struct TreeNode {
      int val;
      TreeNode *left;
      TreeNode *right;
      TreeNode(int x) : val(x), left(NULL), right(NULL) {}
      };

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
     cout<<"size"<<tree.size();
        for(int i =0; i< tree.size();i++){
            //cur->left = NULL;
            cur->right = new TreeNode(tree[i]);
            cur = cur->right;
        }
        return r1->right;
    }

    void midorder(TreeNode * root,vector<int> &tree){
        if(!root) return;
        midorder(root->left,tree);
        tree.push_back(root->val);
        cout <<" jiedian"<< " "<< root->val<<" ";
        midorder(root->right,tree);
    }

};

void trimLeftTrailingSpaces(string &input) {
    input.erase(input.begin(), find_if(input.begin(), input.end(), [](int ch) {
        return !isspace(ch);
    }));
}

void trimRightTrailingSpaces(string &input) {
    input.erase(find_if(input.rbegin(), input.rend(), [](int ch) {
        return !isspace(ch);
    }).base(), input.end());
}

TreeNode* stringToTreeNode(string input) {
    trimLeftTrailingSpaces(input);
    trimRightTrailingSpaces(input);
    input = input.substr(1, input.length() - 2);
    if (!input.size()) {
        return nullptr;
    }

    string item;
    stringstream ss;
    ss.str(input);

    getline(ss, item, ',');
    TreeNode* root = new TreeNode(stoi(item));
    queue<TreeNode*> nodeQueue;
    nodeQueue.push(root);

    while (true) {
        TreeNode* node = nodeQueue.front();
        nodeQueue.pop();

        if (!getline(ss, item, ',')) {
            break;
        }

        trimLeftTrailingSpaces(item);
        if (item != "null") {
            int leftNumber = stoi(item);
            node->left = new TreeNode(leftNumber);
            nodeQueue.push(node->left);
        }

        if (!getline(ss, item, ',')) {
            break;
        }

        trimLeftTrailingSpaces(item);
        if (item != "null") {
            int rightNumber = stoi(item);
            node->right = new TreeNode(rightNumber);
            nodeQueue.push(node->right);
        }
    }
    return root;
}

string treeNodeToString(TreeNode* root) {
    if (root == nullptr) {
      return "[]";
    }

    string output = "";
    queue<TreeNode*> q;
    q.push(root);
    while(!q.empty()) {
        TreeNode* node = q.front();
        q.pop();

        if (node == nullptr) {
          output += "null, ";
          continue;
        }

        output += to_string(node->val) + ", ";
        q.push(node->left);
        q.push(node->right);
    }
    return "[" + output.substr(0, output.length() - 2) + "]";
}

int main() {
    string line;
    freopen("in.txt","r",stdin);
    while (getline(cin, line)) {
        TreeNode* root = stringToTreeNode(line);

        TreeNode* ret = Solution().increasingBST(root);

        string out = treeNodeToString(ret);
        cout << out << endl;
    }
    return 0;
}

```