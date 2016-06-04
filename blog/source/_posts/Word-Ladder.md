title: Word Ladder
date: 2016-06-03 21:33:33
tags: Algorithm, BFS, Lintcode
---
[Question Link](http://www.lintcode.com/en/problem/word-ladder/)
##Intuition
it is first to build the distance matrix between each word and use shortest path algorithm to find the path from the start node to the destination.

##Problem 
We assume we have N words, it needs O(N^2) to build the matrix and O(N^2) space to store the matrix. The length of word is L, therefore it is O(L*N^2) algorithm. Obviously it will be timeout for large test set.
##Solve
I notice that we only need the pair of words with 1 character difference. We only need build the adjacent list of such node that satisfy the criteria.
For the BFS, we can abandon the node when its current path is longer than the current shortest path.
However those optimization does not influence the time  and space complexity. Consequently it is still timeout for largest data set.

```c++
// compute the difference between pair of words
int dis(string w1,string w2){
    int diff = 0;
    for(int i = 0; i < w1.size(); i++){
        if(w1[i] != w2[i]){
            diff ++;
        }
        if(diff == 2){ // early stop
            break;
        }
    }
    return diff;
}

int bfs(unordered_map<int,vector<int> > &matrix, int start,int end, int n){
    int shortest = INT_MAX;
    queue< pair<int,int> > Q;
    Q.push({start,1});
    vector<int> visted(n,0);
    while(!Q.empty()){
        int cur = Q.front().first;
        visted[cur] = 1;
        int length = Q.front().second;
        if(length > shortest){ // early stop
            Q.pop();
            continue;
        }
        // find the destination
        if(cur == end && length < shortest){ 
            shortest = length;
            Q.pop();
            continue;
        }
        
        Q.pop();
        for(int i = 0; i < matrix[cur].size(); i++){
            if(visted[matrix[cur][i]] == 0){
                Q.push({matrix[cur][i],length + 1});
            }
        }
    }
    return shortest == INT_MAX ? 0 :shortest;
}
int ladderLength(string start, string end, unordered_set<string> &dict) {
    unordered_map<int,vector<int> > matrix;
    // write your code here
    int n = dict.size() + 2;
    vector<string> words(dict.begin(),dict.end());
    words.push_back(end);
    words.push_back(start);
    int startPos = words.size() - 1;
    int endPos = words.size() - 2;
    // build the adjacent list
    for(int i = 0; i < words.size(); i ++){
        vector<int> adj;
        for(int j = 0; j < words.size(); j ++){
            int c = dis(words[i],words[j]);
            if(c == 1){
                adj.push_back(j);
            }
        }
        matrix[i] = adj;
    }
    return bfs(matrix, startPos, endPos, n);
}
Why shell we change the way to search
```
```c++
// reference code from http://www.jiuzhang.com/solutions/word-ladder/
int ladderLength(string start, string end, unordered_set<string> &dict) {
    // write your code here
    if(start == end){
        return 1;
    }
    queue<string> Q;
    Q.push(start);
    dict.erase(start);
    int len = 2;
    while(!Q.empty()){
        int n = Q.size();
        for(int i = 0; i < n;i ++){
            string curr = Q.front();
            Q.pop();
            for(int j = 0; j < curr.size(); j ++){
                char pre = curr[j];
                // change the character to search
                for(char c = 'a'; c <= 'z';c++){
                    if(c == pre) continue;
                    curr[j] = c;
                    if(curr == end){
                        return len;
                    }
                    // O(1) to check
                    auto result = dict.find(curr);
                    if(result != dict.end()){
                        Q.push(*result);
                        dict.erase(curr);
                    }
                }
                curr[j] = pre; // change back
            }
        }
        len ++;
    }
    return 0;
}
```