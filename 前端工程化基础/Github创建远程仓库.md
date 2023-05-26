## 常规操作

> 这个方案很麻烦

1. 创建远程仓库

2. 在本地文件夹 `git init` 创建本地仓库

3. 修改代码

4. `git add .`

5. `git commit -m ""`

6. `git remote add origin <远程仓库地址>`

7. `git fetch`

8. `git branch --set-upstream-to=origin/main`

9. `git merge --allow-unrelated-histories`

10. `git config push.default upstream`

11. `git push`



## 简便写法

> 简单许多了 --

1. 创建远程仓库

2. 在本地文件夹 `git init` 创建本地仓库

3. 修改代码 

4. `git add .`

5. `git commit -m ""`

6. `git remote add origin <远程仓库地址>`

7. `git fetch`

8. `git checkout main`

9. `git push`

   

## 最简方案

> 没啥问题就用这个方案吧 !

1. 创建一个远程仓库
2. 在本地文件夹下 `git clone <远程仓库地址>`
3. 修改代码
4. `git add .`
5. `git commit -m ""`
6. `git push`
