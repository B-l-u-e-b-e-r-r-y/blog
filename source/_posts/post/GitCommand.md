---
title: 簡單整理 Git 常用指令
date: 2020-03-07
comments: true
categories: 
- Git
---

# 簡單整理 Git 常用指令

## 新增檔案

`.` 是全部新增的意思，也可以只新增部分檔案。

```
git add .
```

---------------------------------------

## 提交檔案

```
git commit -m "內容"
```

---------------------------------------

## 推送檔案
```
git push
```

---------------------------------------

## 拉取檔案

```
git pull
```

---------------------------------------

## Clone 遠端數據庫

```
git clone git位置
```

---------------------------------------

## 查看目前分支

```
git branch
```

---------------------------------------

## 新增分支

```
git branch 分支名稱
```

---------------------------------------

## 切換到該分支

```
git checkout 分支名稱
```

---------------------------------------

## 強制回到和保存端一模一樣的檔案（慎用，檔案有異常時再使用）

```
git fetch --all
git reset --hard origin/master
```

---------------------------------------

## 查詢所有做過的 git 動作，可查詢到每一個動作的編號

```
git reflog
```

---------------------------------------

## 回覆之前的某一個操作

```
git reset 編號 --hard
```

---------------------------------------

## 使用 rebase 進行合併

```
git rebase 分支名稱
```

---------------------------------------

## rebase 後回復

有兩種方式，第一種方式必須先用 `git reflog` 查詢，dd1f0f8 是 rebase 前的一個動作編號：

```
git reset dd1f0f8 --hard
```

第二種方式是使用 ORIG_HEAD：

```
git reset ORIG_HEAD --hard 
```

---------------------------------------

## 參考資料

* [另一種合併方式（使用 rebase）｜高見龍](https://www.youtube.com/watch?v=HeF7dwVyzow&feature=emb_err_watch_on_yt)
* [Git & GitHub 教學手冊｜六角學院](https://w3c.hexschool.com/git/cfdbd310?fbclid=IwAR0cO3-A9voMoOPIA-qnZWbm2o7TNICwsJc4J0rnbZxCcNy8sBVs7NU9OmQ)