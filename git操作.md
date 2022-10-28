# 基本操作

## git init

初始化一个git仓库

```bash
git init
```

这时会生成一个 .git 文件夹

## git status 

查看当前文件状态，包括未提交、待提交文件

## 忽略文件

在文件夹下创建`.gitignore`，这个文件夹中便是我们要忽略的文件，

例子：

```js
// 注释写法：
# 注释内容 #
// 忽略所有以.txt结尾的文件
*.txt
// 忽略指定文件
Hello.html
// 忽略指定为文件夹
node_modules
// 忽略名称中以modules结尾的文件夹
*modules/
// 忽略名称中以node开头的文件夹
node*/
// 忽略名称中间包含_modu的文件夹
*_modu*/
```

## git add

`git add . `

将文件提交到暂存区

## git commit -m "描述"

`git commit -m "描述语句"`

将文件提交到待提交区

## git push

将待提交区的文件提交到远程仓库

## git clone url

将远程仓库的文件克隆下来

# 分支操作

## 列出分支

`git branch`

## 创建分支

`git branch dev`

## 切换分支

`git checkout dev`

## 删除分支

`git branch -d dev`

## 删除远程分支

`git push origin --delete dev`

## 合并指定分支到当前分支

`git merge dev`

# 拉取操作

## 拉取最新项目

`git pull`

