## Jumpserver API 添加删除用户

### 1.创建Access Key
- 使用`administrator`角色的用户创建`Access Key`
- 点击`头衔`选择`API Key`
- 创建`Access Key`

### 2. API 认证
- `Jumpserver`支出四种认证方式
	+ Session
	+ Token 
	+ Private Token
	+ Access Key
- 我们在这里使用`Access Key`方式
- 其他方式不太合适
- 如果感兴趣可以参考[官方文档](https://docs.jumpserver.org/zh/master/dev/rest_api/)
- `Jumpserver`官方还维护了一个`sdk`，但开发者太忙维护的不是太好
- 官方示例的认证函数：

```python
def get_auth(KeyID, SecretID):
    signature_headers = ['(request-target)', 'accept', 'date']
    auth = HTTPSignatureAuth(key_id=KeyID, secret=SecretID, algorithm='hmac-sha256', headers=signature_headers)
    return auth
```

- 后面使用`requests`请求接口是需带上该`auth`对象
- eg: `res = equests.get(url, params=params, auth=auth, headers=headers)`

### 3.API接口
- 相关的文档可以查看自己的`http://<url>/api/docs/`
- 可以看到具体的`接口`和具体的`model`
- 已`创建用户`为例：
- 接口为：`/api/v1/users/users/`
- `talk is cheap show me the code`

```python
class JmsUser():

    def __init__(self, email, name, groups_name):
        self.jms_url = 'http://jms.tarsocial.com'
        self.api_url = self.jms_url + '/api/v1/users/users/'
        self.email = email
        self.name = name
        self.groups_name = groups_name
        self.auth = self.get_auth()
        self.headers = self.get_headers()
        self.groups_ids = self.get_groups_ids()


    def is_success_res(self, res):
        if res.status_code in [200, 201] and res.json():
            return True
        else:
            return False

    def get_groups_ids(self):
        url = self.jms_url + '/api/v1/users/groups/'
        params = {'name': self.groups_name}
        res = requests.get(url, params=params, auth=self.auth, headers=self.headers)
        if self.is_success_res(res):
            print("Find the group name [{}] id [{}].".format(self.groups_name, res.json()[0].get('id')))
            return [res.json()[0].get('id')]
        else:
            print("The Group [{}] not find, will set [].".format(self.groups_name))
            return []

    def get_auth(self):
        KeyID = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'
        SecretID = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'
        signature_headers = ['(request-target)', 'accept', 'date']
        auth = HTTPSignatureAuth(key_id=KeyID, secret=SecretID, algorithm='hmac-sha256', headers=signature_headers)
        return auth

    def get_headers(self):
        gmt_form = '%a, %d %b %Y %H:%M:%S GMT'
        headers = {
            'Accept': 'application/json',
            'X-JMS-ORG': '00000000-0000-0000-0000-000000000002',
            'Date': datetime.datetime.utcnow().strftime(gmt_form)
        }
        return headers

    def is_exist_user(self):
        params = {'username': email}
        res = requests.get(self.api_url, params=params, auth=self.auth, headers=self.headers)
        if self.is_success_res(res):
            print("JumpServer username [{}] already exist.\n".format(email))
            return True
        else:
            print("JumpServer username [{}] not exist, will create.\n".format(email))
            return False

    def create_user(self):
        if not self.is_exist_user():
            data = {
                'name': name,
                'username': email,
                'email': email,
                'is_active': True,
                'groups': self.groups_ids
            }
            res = requests.post(self.api_url, json=data, auth=self.auth, headers=self.headers)
            if self.is_success_res(res):
                print("JumpServer username [{}] create success.\n".format(email))
            else:
                print("JumpServer username [{}] create failed.\n".format(email))
                print(res, res.json())

    def delete_user(self):
        params = { 'username': email }
        res = requests.delete(self.api_url, params=params, auth=self.auth, headers=self.headers)
        if res.status_code is 204:
            print("JumpServer username [{}] delete success.\n".format(email))
        else:
            print("JumpServer username [{}] delete failed.\n".format(email))
            print(res, res.json())
```
