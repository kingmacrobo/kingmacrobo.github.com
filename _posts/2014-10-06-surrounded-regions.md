---
layout: post
category: leetcode
title: Leetcode Surrounded Regions
tagline: by hombo
tags: [algorithm,leetcode,c++,DFS]
---
这是一道图搜索题，关键在于用DFS或BFS搜索时，处理好边界条件和其他限制条件，例如遇到`‘X’`就停止这条路的搜索。

<!--more-->

##原文题目：##

Given a 2D board containing `'X'` and `'O'`, capture all regions surrounded by `'X'`.

A region is captured by flipping all 'O's into `'X'`s in that surrounded region.

For example,

	X X X X
	X O O X
	X X O X
	X O X X

After running your function, the board should be:

	X X X X
	X X X X
	X X X X
	X O X X

##分析：##

题目的意思是说把这个2D board中完全被`'X'`包住的`'O'`的区域全部替换为`'X'`，就像上面给出的一个例子，内部的三个`'O'`被替换了，而边界的`'O'`由于没有被包住而不需要替换。

这道题其实就是一个图的搜索问题，可以把这个2D board看成由几个连通子图构成的，连通子图由全`O`或者全`X`构成

搜索的过程是这样的：

	1. 从头开始遍历这个2D board， 如果遇到一个`O` ，就从这个`O` 开始进行DFS或者BFS遍历它的连通子图，边界条件就是遇到`X`或者board的边界停止。我们知道连通子图之间是没有路径的，这样就不会对其他子图产生影响。

	2. 在遍历的过程中，不断判断是否有`O`在board的边界，如果有，就标记这个子图为`需要替换为‘X’的子图`，在遍历结束后将这个子图中的元素全部替换成`X`。子图的存储可以用一个vector实现。

	3. 然后继续在board中往后找没有被访问过的`O`，继续上面的过程，直至结束。
	
这样我们就完成了题目的要求。

这里用`DFS`和`BFS`都行，时间复杂度是O(n)，由于要借助一定空间来做标记和存储子图，所以空间复杂度是O(n)

这个只给出用`DFS`AC的代码，`BFS`的实现方式把stack换成queue就行了。

	class Solution {
	public:
	    void solve(vector<vector<char>> &board) {
	        if(board.size()<=2||board[0].size()<=2) return;
	        stack<pair<int,int>> s; //DFS stack
	        int xsize = board.size(), ysize = board[0].size();
	        vector<vector<bool>> record(xsize,vector<bool>(ysize,false)); // 访问标记
	        vector<pair<int,int>> canlist; //存储连通子图
	        bool can = true;
	        for(int i = 1 ; i < xsize-1 ; ++i){
	            for(int j = 1 ; j < ysize-1 ; ++j){ //遍历所有未访问的‘O’
	                if(board[i][j] == 'O'&&!record[i][j]){
	                    can = true;
	                    s.push(make_pair(i,j));
	                    canlist.push_back(make_pair(i,j));
	                    record[i][j] = true;
	                    while(!s.empty()){ //DFS
	                        pair<int,int> p = s.top();
	                        s.pop();
	                        if(can&&(p.first==0||p.first==xsize-1||p.second==0||p.second==ysize-1))
	                            can = false;
	                        if((p.first>0)&&board[p.first-1][p.second]=='O'&&!record[p.first-1][p.second]){
	                            s.push(make_pair(p.first-1,p.second));
	                            record[p.first-1][p.second] = true;
	                            canlist.push_back(make_pair(p.first-1,p.second));
	                        }
	                        if((p.first<xsize-1)&&board[p.first+1][p.second]=='O'&&!record[p.first+1][p.second]){
	                            s.push(make_pair(p.first+1,p.second));
	                            record[p.first+1][p.second] = true;
	                            canlist.push_back(make_pair(p.first+1,p.second));
	                        }
	                        if((p.second>0)&&board[p.first][p.second-1]=='O'&&!record[p.first][p.second-1]){
	                            s.push(make_pair(p.first,p.second-1));
	                            record[p.first][p.second-1] = true;
	                            canlist.push_back(make_pair(p.first,p.second-1));
	                        }
	                        if((p.second<ysize-1)&&board[p.first][p.second+1]=='O'&&!record[p.first][p.second+1]){
	                            s.push(make_pair(p.first,p.second+1));
	                            record[p.first][p.second+1] = true;
	                            canlist.push_back(make_pair(p.first,p.second+1));
	                        }
	                    }
	                    if(can){ //改变连通子图中的'O'为‘X’
	                        for(int w = 0 ; w < canlist.size() ; ++w)
	                            board[canlist[w].first][canlist[w].second] = 'X';
	                    }
	                    canlist.clear();
	                }
	            }
	        }
	    }
	};



