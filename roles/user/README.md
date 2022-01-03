# user

## Role Variables

- `host_users`: `array`, 主机用户列表
  - `name`: `string`, 用户名
  - `password`: 可选，`string`, 密码 hash, 最终会被写入 `/etc/passwd`
    文件，生成方法见下文
  - `shell`: 可选，`string`, 用户 shell
  - `group`: 可选，`string`, 用户的主用户组，默认为同名用户组
  - `groups`: 可选，`array`, 用户的所有用户组，默认至少有一个同名用户组
  - `state`: 可选，`string`, 是否保留用户，默认为 `present`
- `user_default_shell`: 可选，`string`, 用户默认 shell，默认值为 `/bin/bash`

### Defaults

- `user_groups`: `array`, 用户组列表，默认会创建列表中的用户

## 添加一个新用户

### 生成用户密码 hash

```sh
ansible all -i localhost, -m debug -a \
  "msg={{ '密码明文' | password_hash('sha512', 'sweetsalt') }}"
```

### 填写用户信息

[user_eg]: ../../inventories/demo/host_vars/demo.yml

在 `host_vars` 文件中填写用户信息，例：[demo.yml][user_eg]

### 添加用户公钥

在 `files/public_keys/` 目录中添加用户公钥文件，文件名格式为 `${用户名}.pub`，例：

## 删除一个用户

把相应 host 的 `host_users` 中的用户 `state` 属性值改为 `absent`。

## 执行变更到主机

role 被执行时，会按照 host 文件定义的 `host_users`，将用户信息同步到目标主机上。例：

```sh
ansible-playbook -K -i inventories/demo/hosts.ini playbooks/user.yml
```
