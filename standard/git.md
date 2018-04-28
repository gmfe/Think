# 观麦git工作流规范

Git 作为一个源码管理系统，不可避免涉及到多人协作。

协作必须有一个规范的工作流程，让大家有效地合作，使得项目井井有条地发展下去。

### 说明：

![suggest][Suggest Icon] 表示**建议**，不要求。

## 目录

- [分支命名](#分支命名)

## 分支命名

```
分支命名规范：
`master`:
主分支， 用于跟踪线上分支

`hotfix-xxx`:
用于修复bug的分支, 修复好合并至`master`后便可以删除

`feature-xxx`:
功能需求类分支, 注意要定期同步一下`master`, 分支feature-xxxx应长期保留，用于记录开发历史

`release-xxx`：
用于提pr的分支，merge至master后便可删除。

`online-xxx`：
类似`master`分支可独立跑在生产环境，但未来不打算merge到`master`的分支。
```

更多扩展：
- [Git工作流程介绍](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)
- [Git相关学习资料](https://github.com/xirong/my-git)

[Suggest Icon]: https://rawgit.com/gmfe/standard/master/svg/min-tuijian.svg