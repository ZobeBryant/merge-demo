# 1. 本demo目的是测试分支合并
## 1.1 什么情况下不冲突
**过程：**

1. 创建主分支main，创建main.js文件。
2. main下创建分支A，新增A.js文件。main合并A分支，无冲突！
3. main下创建分支B，新增B.js文件。main合并B分支，无冲突！

**结论：** 不同分支操作不同文件，合并时不会产生冲突。
## 1.2 什么情况下会产生冲突
**过程：**
1. 在B分支下修改了main.js文件。main合并B分支，无冲突！
2. 在A分支下修改了main.js文件。main合并A分支，有冲突！
   
**结论：** 不同分支操作相同文件，合并时可能会产生冲突。

# 2. 关于merge
merge算法分为：Fast Forward Merge 和 3-way Merge
## 2.1 Fast Forward Merge算法
当我们从 master checkout feature 分支进行开发，如果之后 master 都没有新的改动，那么当我们的 feature 分支和入 master 的时候，git 就会使用 fast forward 的方式进行 merge.

```
    master
       |
M1 --- M2
       \
        \--- F1
             |
           feature
```
## 2.2 3-way Merge算法
当我们从 master checkout feature 分支进行开发，如果之后 master 又有了额外的改动 (M3)，这时 master 和 feature 是有分叉的，因此 git 只能使用 3-way merge 的方式进行 merge.
```
            master
              |
M1 --- M2 --- M3 
       \             
        \--- F1 
              |
           feature
```
首先 3-way 的含义是 merge 操作需要 3 个 commit

1. feature 分支 pointer，即 F1
2. master 分支 pointer，即 M3
3. feature 分支和 master 分支的最近的公共祖先节点 (common ancestor)，即 M2

整个流程：

1. 首先需要找到 the most recent common ancestor
2. 拿到 F1 & M2 的 diff，和 M3 & M2 的 diff
3. 一个个过 diff
    1. 如果两边的 diff 在某个位置都进行了改动，并且不一致，标记为冲突
    2. 如果一边的 diff 在某个位置进行了改动，而另外一边没有进行改动，那么保留改动的一方
   
最终的结果：流程结束后，git 会创建一个 merge commit (M4)，并将 master 分支指向它。

```
                  master
                     |
M1 --- M2 --- M3 --- M4
       \             /
        \--- F1 --- /
              |
           feature
```
