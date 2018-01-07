---
title: Git 忽略文件总结
date: 2016-08-15 10:58:32
tags:
- tech
- git
---

在git中，我们有时需要在repo中忽略某些文件，比如配置文件，dependency，编译生成的文件等。在不同的场景下，需要采用不同的方法。

## 加入 .gitignore

最常用的方法就是把需要ignore的文件名添加到 .gitignore 文件中。.gitignore 文件本身也要放到git的目录中。github提供了[各种常见语言和项目的 .gitignore 模板](https://github.com/github/gitignore) , 建议在项目刚刚创建时，就加入一个完善的 .gitignore。

### 添加已经track的文件到 .gitignore
有时，一些文件已经被加入到repo里，之后又被加入到 .gitignore 文件里。在运行 `git status` 时，会发现不起效果。此时，必须要untrack这个文件，才能使修改生效:

    git rm --cached FILE_NAME

## 加入 .git/info/exclude
由于 .gitignore 文件在项目repo里，所有的用户都会看到相同的配置。如果想要在**本地**ignore一些文件，又不想让其他人看到，可以把这些文件加入到 `.git/info/exclude` 里，语法和 .gitignore 的一样。

## 使用 update index --skip-worktree
还有一种特殊情况，很多人都不知道如何处理： 一些文件已经加入被track，本地也有修改。然而，又不想让这些修改被别人看到。比如，在java项目中，我们会创建一些launcher，launcher的param经常需要被替换成本地的文件路径，然而我们不想让这些修改加入repo里。此时可以用如下命令：

    git update-index --skip-worktree my_launcher.launch // works for one file

这个命令的一个局限是只能适用于单个文件。如果有很多文件需要修改，则需要先要找到他们，然后再运行命令：

    find ./ -name "*.launch" -exec git update-index --skip-worktree '{}' \; // works for multi files

有一点需要注意的是，当用 `git pull` 更新项目时，这些文件依然会被update。

还有另外一个命令 `git update index assume-unchanged` 也可以达到类似的效果，但是两者的原理不同。[stackoverflow上的这个问题](http://stackoverflow.com/questions/13630849/git-difference-between-assume-unchanged-and-skip-worktree) 给出了详细的解释。

如果想要undo这项操作，只需要把 `--skip-worktree` 替换成 `--no-skip-worktree` 即可。
