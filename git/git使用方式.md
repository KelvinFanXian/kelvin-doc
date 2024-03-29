#  git开发单人，和多人开发模式



由于我们的项目（每个仓库）需要参与的开发人数很少，甚至很多项目只有一个开发者，不是很需要gitflow，我们制定属于自己的敏捷开发模式，应遵循如下规范：

1.master为主分支，master保持与线上最新版本同步

2.单个仓库的多个项目可同时进行开发，互不干涉

3.应保证各环境的代码一致

4.master提交点清晰，每一个点就代表一个功能（功能分支开发测试完成了才合并到master），能做到单点回滚

以下列出2种方式：

一、单人开发模式

## 适用范围：某个代码仓库有且只有一位开发者、或者某个功能只有一个开发者

分支开发流程：

1.拉取master分支为最新

2.基于master建立新功能分支，命名方式为：个人拼音名/功能名（如果是某个仓库只有一个人在开发且维护命名方式直接使用功能名）

3.开发

4.提测：测试直接拉取功能分支去测试

5.测试通过把功能分支reset为一个点再合并到master

6.发布master

注意：虽然为单人开发，但也有可能在开发A功能的时候线上紧急修复了一个bug（master分支有变更），此时应及时进行rebase（就是把最新master的分支的变更合并到了功能开发分支）

已上线的分支需要及时删除（清理）

二、多人开发模式

## 适用范围：某个功能需要多人同时进行开发

1.拉取master分支为最新

2.基于master建立组分支，组分支命名方式为：`group/功能名`

3.参与的开发者需要基于组分支建立个人分支用于开发（不建议直接在组分支开发），个人分支命名方式为：`你的姓名拼音/功能名`

4.开发在个人分支开发

5.某一个功能开发完成时可以合并到组分支（**合并前**防止其他同事有更新**应先rebase**）

6.当所有的子功能开发完成时可提测，直接拉组分支测试（测试前应把组分支同master rebase，防止master有更新，应保证测试的是最新的要上线的代码）

7.测试通过把组分支reset到一个点并且合并到master

8.发布master

注意：个人分支一定要同组分支进行rebase、组分支一定要同master进行rebase

个人分支rebase的时间点为：某个子功能开发完成、或者团队之间有文件需要依赖、或者工作天下班做一个rebase

组分支的rebase的时间点为：按天、或者按里程碑、提测一定要做rebase、上线前

已上线的分支需要及时删除（清理）



---

## ref

- https://www.yiibai.com/git/git_rebase.html