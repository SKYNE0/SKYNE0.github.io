## Gitlab API 创建用户

### 1.创建Personal Access Tokens
- 该用户需要具有`Admin`权限
- 前往`settings`，选择`Access Tokens`
- 创建一个`Personal Access Tokens`
- 创建完成后会返回该`token`，记下来保存好，页面刷新后`无法找回`

### 2.Gitlab API 
- `Gitlab`提供了非常丰富的`API`接口：[Interactive API documentation](https://docs.gitlab.com/ee/api/index.html)
- 已创建普通用户为例：

```bash
# curl -XPOST -H "PRIVATE-TOKEN: XXXXXXXXXXXXX" -H 'content-type: application/json' "http://git.tarsocial.com/api/v4/users" -d '{ "email": "shaokui.ma@tarsocial.com", "name": "shaokui.ma@tarsocial.com", "username": "shaokui.ma", "reset_password": "true", "can_create_group": "false" }'

{"name":"shaokui.ma@tarsocial.com","username":"shaokui.ma","id":188,"state":"active","avatar_url":"http://www.gravatar.com/avatar/5152aadea07a89cb34b8f5dae41c96d1?s=80&d=identicon","web_url":"http://git.tarsocial.com/shaokui.ma","created_at":"2022-01-13T03:36:40.162Z","bio":null,"location":null,"skype":"","linkedin":"","twitter":"","website_url":"","organization":null,"last_sign_in_at":null,"confirmed_at":null,"last_activity_on":null,"email":"shaokui.ma@tarsocial.com","color_scheme_id":1,"projects_limit":50,"current_sign_in_at":null,"identities":[],"can_create_group":false,"can_create_project":true,"two_factor_enabled":false,"external":false}
```

- 其他诸如`删除`、`禁用`等操作与此类此，参考文档即可

### 3.脚本化

```python
class GitlabUser():
    def __init__(self, email, name):
        self.username = email.split('@')[0]
        self.email = email
        self.name = name
        self.url = 'http://git.tarsocial.com'
        self.api_url = self.url + '/api/v4/users/'
        self.headers = {
            'PRIVATE-TOKEN': 'XXXXXXXXXXXXX',
            'content-type': 'application/json'
        }

    def is_success_res(self, res):
        if res.status_code in [200, 201] and res.json():
            return True
        else:
            return False

    def is_exist_user(self):
        params = {'username': self.username}
        res = requests.get(self.api_url, params=params,  headers=self.headers)
        if self.is_success_res(res):
            print("Gitlab username [{}] already exist.\n".format(self.email))
            return True
        else:
            print("Gitlab username [{}] not exist, will create.\n".format(self.email))
            return False

    def get_user_id(self):
        if self.is_exist_user():
            url = self.url + '/api/v4/users?username=' + self.username
            res = requests.get(url, headers=self.headers)
            if self.is_success_res(res):
                id = res.json()[0].get('id')
                print("Gitlab username [{}] of id is {}.\n".format(self.email, id))
                return id
            else:
                print("Gitlab username [{}] of id is none\n".format(self.email))
                return False

    def create_user(self):
        if not self.is_exist_user():
            data = {
                'email': self.email,
                'name': self.name,
                'username': self.username,
                'reset_password': 'true',
                'can_create_group': 'false'
            }
            res = requests.post(self.api_url, json=data, headers=self.headers)
            if self.is_success_res(res):
                print("Gitlab username [{}] create success.\n".format(self.email))
            else:
                print("Gitlab username [{}] create failed.\n".format(self.email))
                print(res, res.json())

    def block_user(self):
        id = self.get_user_id()
        url = self.api_url + str(id) + '/block'
        res = requests.post(url, headers=self.headers)
        if self.is_success_res(res):
            print("Gitlab username [{}] block success.\n".format(self.email))
        else:
            print("Gitlab username [{}] block failed.\n".format(self.email))
```