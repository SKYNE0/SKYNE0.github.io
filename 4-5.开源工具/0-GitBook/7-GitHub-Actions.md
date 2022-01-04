## GitHub-Actions

### 1.GitHub-Actions 是什么
- [官方文档](https://docs.github.com/cn/actions)
- `GitHub-Actions` 是 `GitHub` 的推出的`CI\CD持续集成服务`
- 使用过熟悉`Jenkins`、`Gitlab-CICD`的应该会很容易理解
- `概念`都是相通的，可能只是`命名`不太一样

### 2.Marketplace Actions
- [Marketplace Actions](https://github.com/marketplace?type=actions)
- `GitHub` 官方推出的可以分享 `Actions` 的地方
- 开发者可以将自己的`Action`存放到`代码仓库`中，其他的开发者可以引用该`Action`
- 如果你需要某个`Action`，不必自己写复杂的脚本
- 直接引用他人写好的`Action`即可，整个持续集成过程，就变成了一个`Action`的组合。
  

### 3.基本概念
- 1.`workflow`: 一个`workflow`工作流就是一个完整的过程，每个`workflow`包含一组`jobs`任务
- 2.`job` : `jobs`任务包含一个或多个`job` ，每个 `job`包含一系列的 `steps `步骤
- 3.`step` : 每个 `step` 步骤可以执行指令或者使用一个 `action` 动作
- 4.`action` : 每个 `action` 动作就是一个通用的基本单元

### 4.workflow文件
- `GitHub-Actions`的配置文件为`仓库根目录`下的`.github/workflows`中的`yml`文件
- `语法`: [Workflow syntax for GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions)
