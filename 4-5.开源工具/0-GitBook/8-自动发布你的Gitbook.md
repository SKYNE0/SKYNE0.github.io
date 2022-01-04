## 自动发布你的Gitbook

### 1.发布步骤
- 1.`Github`已配置好 `Github-Pages`
- 2.`Github`设置`Personal access tokens`
- 3.仓库设置`Actions secrets`
- 4.创建`Github Actions`的配置文件
- 5.推送代码

#### 1.1配置`Github-Pages`
- 略

#### 1.2配置`Personal access tokens`
- [创建个人访问令牌](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
- 参考上述文档进行创建`Personal access tokens`，注意设置过去时间，最好定期更换`Personal access tokens`
- `Personal access tokens`可以代替`命令行`或者`API`中的`密码`，因此一定要注意`Personal access tokens`的`安全性`

#### 1.3配置`Actions secrets`
- `secrets`是项目中的`加密环境变量`
- 可以将配置的`Personal access tokens`的`值`作为`secrets`从而可以被`Actions`脚本读取到，获取`权限`推送代码
- 在 `username.github.io`仓库中的`settings`、`secrets`可以设置一个`Actions secrets`
- 创建时`Name`字段建议为: `ACCESS_TOKEN`, `Value`即为`Personal access tokens`的`值`

#### 1.4创建`Github Actions`的配置文件
- 在这里我们使用[Github Action For Gitbook](https://github.com/ZanderZhao/gitbook-action/)来定义我们的`workflow`
- 在`仓库根目录`下的新建`.github/workflows`目录并添加`gitbook-action.yml`文件

```
gitbook-action.yml

name: 'Gitbook Action Build'
on:
  push:
    branches:
      - master  # trigger branch
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout action
      uses: actions/checkout@v2
    - name: Gitbook Action           # https://github.com/ZanderZhao/gitbook-action/releases
      uses: ZanderZhao/gitbook-action@v1.2.4  # -> or ZanderZhao/gitbook-action@master.  If not use master click above, use latest please 
      with:                                   #    or fork this repo and use YourName/gitbook-action@master
        token: ${{ secrets.ACCESS_TOKEN }}  # -> remember add this in settings/secrets as following
```

#### 1.5.推送代码
- 推送代码: 略
- 查看`username.github.io`仓库中的 `Actions`页面即可看到正在运行的`workflows`
- `workflow`运行失败与否都会邮件通知你
- 至此，就可以专注于写作