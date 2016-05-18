title: Validate Binary Search Tree
date: 2016-05-18 13:11:30
tags: Online Judge, BST
---
# Validate Binary Search Tree
--------
The binary search tree is defined as following
1. nodes in the left subtree are less than the root
2. nodes in the right subtree are greater than the root

### How to validate a binary search tree

Idea 1.	the maximum of left tree is less the root and the minimum of right tree is greater than the root

[lintcode](http://www.lintcode.com/en/problem/validate-binary-search-tree/)
[leetcode](https://leetcode.com/problems/validate-binary-search-tree/)

~~~C++
#define MAX(a,b) (a) > (b) ? (a):(b)
#define MIN(a,b) (a) < (b) ? (a):(b)
bool isValidBST(TreeNode *root) {
        // write your code here
        if(root == nullptr){
            return true;
        }
        if(root->left == nullptr && root->right == nullptr){
            return true;
        }
        return (getMax(root->left) < root->val) && getMin(root->right) > root->val 
        && isValidBST(root->left) && isValidBST(root->right);
    }
    long long getMax(TreeNode *root){
        if(root == nullptr){
            return LONG_MIN;
        }
        return MAX(root->val,MAX(getMax(root->left),getMax(root->right)));
    }
    long long getMin(TreeNode *root){
        if(root == nullptr){
            return LONG_MAX;
        }
        return MIN(root->val,MIN(getMin(root->left),getMin(root->right)));
    }
~~~
The problem is the method is too slow. It is a O(N^2) algorithm because each node does not memory the largest value and minimum value so far therefore we need check every time.

Idead 2. Pass a minimum and maximum value as parameter,each time call reduce the minimum to the root's value -1 and the maximum to the root's value + 1.

~~~C++
	bool _validBSTMinMax(TreeNode *root,long long min,long long max){
        if(root != nullptr){
            bool valid = (root->val >= min) && (root->val <= max);
            if(!valid) return false;
            return _validBSTMinMax(root->left,min,(long long)root->val - 1) && 
            		_validBSTMinMax(root->right,(long long)root->val + 1,max);
        }else{
            return true;
        }
    }
   	bool isValidBST(TreeNode* root) {
        if(root != nullptr){
            return _validBSTMinMax(root,LLONG_MIN,LLONG_MAX);
        }
        return true;
    }
~~~
Optimized version: to avoid every time calculation the max and min, it is much more quicker in leetcode.

~~~C++
	bool _validBSTMinMax(TreeNode *root,long long min,long long max){
        if(root != nullptr){
            bool valid = (root->val > min) && (root->val < max);
            if(!valid) return false;
            return _validBSTMinMax(root->left,min,(long long)root->val) && 
            		_validBSTMinMax(root->right,(long long)root->val,max);
        }else{
            return true;
        }
    }
   	bool isValidBST(TreeNode* root) {
        if(root != nullptr){
            return _validBSTMinMax(root,LLONG_MIN,LLONG_MAX);
        }
        return true;
    }
~~~