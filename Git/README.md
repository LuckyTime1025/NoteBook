# Git 学习笔记

## 设置用户信息

```shell
git config --global user.name "Lucky"
git config --global user.email "luckytime1025@gmail.com"
```

## 代理配置

```bash
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy https://127.0.0.1:7890
git config --global http.proxy 'socks5://127.0.0.1:7890'
git config --global https.proxy 'socks5://127.0.0.1:7890'

git config --global https.proxy https://username:password@proxy.baidu.com:7890
```

## 取消代理

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## 初始化仓库

```shell
git init
```

## 添加文件到暂存区

```shell
git add
```

## 提交到仓库

```shell
git commit -m "commit message"
```

## 查看文件状态

```shell
git status
```

## 查看提交日志

```shell
git log [option]
```

- option
  - ```--all``` 显示所有分支
  - ```--pretty=oneline``` 单行显示提交信息
  - ```--abbrev-commit``` 缩略显示 ```commitld```
  - ```--graph``` 图形化显示

```shell
git log --pretty=oneline --abbrev-commit --graph
```

## 版本回退

```shell
git reset --hard <commitld>
```

## 查看已经删除的记录

```shell
git reflog
```

> 设置某些文件无需纳入 ```Git``` 管理，例如自动生成的日志文件，或者编译过程中创建的临时文件，可以创建 ```.gitignore``` ,列出需要忽略的文件

---

## 分支

### 查看分支

```shell
git branch
```

### 查看远程仓库关联状态

```shell
git branch -vv
```

### 创建新分支

```shell
git branch <branch name>
```

### 切换分支

```shell
git checkout <branch name>
```

### 创建并切换分支

```shell
git checkout -b <branch name>
```

### 合并分支

```shell
git merge <branch name>
```

> 合并分支需要先切换到目标分支

### 删除分支

```shell
git branch -d <branch name>
git branch -D <branch name>
```

> ```-d``` 需要做各种检查，如果分支暂存区有未提交到仓库中，无法删除  
> ```-D``` 强制删除

### 在开发中，一般有如下分支使用原则与流程

- master 生产分支
  - 线上分支，主分支，中小规模项目作为线上运行的应用对应的分支
- develop 开发分支
  - 是从master创建的分支，一般作为开发部门的主要开发分支，如果没有其他并行开发不同期上线要求，都可以在此版本进行开发，阶段开发完成后，需要合并到master分支，准备上线
- feature/xxx 分支
  - 从develop创建的分支，一般是同期并行开发，但不同期上线时创建的分支，分支上的研发任务完成后合并到develop分支。
- hotfix/xxx 分支
  - 从master派生的分支，一般作为线上bug修复使用，修复完成后需要合并到master、test、develop分支
- 还有其他一些分支，例如test分支（用于代码测试）、pre（预上线分支）分支等等。

## 远程仓库

### 生成```rsa```密匙

```shell
ssh-keygen -t rsa -b 4096 -C "luckytime1025@gmail.com"
```

> 需要在远程仓库中配置 ~/.ssh/id_rsa.pub 中的公匙

### 测试是否配置成功

```shell
ssh -T git@gitee.com
```

### 添加远程仓库

```shell
git remote add <remote name> <remote url>
```

> 远程仓库名称一般配置为```origin```  

### 查看远程仓库

```shell
git remote
```

### 删除远程仓库

```shell
git remote rm <remote name>
```

### 修改远程仓库地址

```shell
git remote set-url <remote name> <remote url>
```

### 推送到远程仓库

```shell
git push [-f] [--set-upstream] [remote name [branch name]:[remote branch name]] 
```

- ```-f``` 强制覆盖
- ```--set-upstream``` 推送到远端的同时并建立起和远端分支的关联关系

> 如果当前分支已经和远端分支关联，则可以省略分支和远端名。

### 从远程仓库克隆

```shell
git clone <remote url>
```

### 从远程仓库抓取

```shell
git fetch [remote name] [branch name]
```

> 如果不指定远程名称和分支名称，则抓取所有分支

### 从远程仓库拉取

```shell
git pull [remote name] [branch name]
```

> pull 是将远程仓库的修改拉到本地并自动进行合并，等同于 ```fetch``` + ```merge```  
> 如果不指定远程名称和分支名称，则抓取所有分支并更新当前分支
