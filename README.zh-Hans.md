# Ansible Infrastructure

[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit&logoColor=white)](https://github.com/pre-commit/pre-commit)

使用 Ansible 配置化、自动化地管理服务器基础设施。

## 目录

- [Ansible Infrastructure](#ansible-infrastructure)
  - [目录](#目录)
  - [安装使用](#安装使用)
    - [已测试过的 Linux 发行版](#已测试过的-linux-发行版)
    - [安装 Ansible](#安装-ansible)
    - [`ansible.cfg`](#ansiblecfg)
    - [Demo](#demo)
    - [执行 playbook](#执行-playbook)
    - [使用 ansible-vault](#使用-ansible-vault)
  - [playbooks](#playbooks)
  - [roles](#roles)
  - [开发环境配置](#开发环境配置)
  - [常用 ad-hoc 命令](#常用-ad-hoc-命令)
    - [`ping`](#ping)
  - [参考](#参考)

## 安装使用

### 已测试过的 Linux 发行版

- Ubuntu Server
  - 18.*
  - 20.*

### 安装 Ansible

```sh
pip install -r requirements-use.pip
```

其他安装方式: [Docs » Installation Guide » Installing Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

### `ansible.cfg`

> Tips: 可用 `ansible-config` 命令查看“运行时”生效的参数值。

```sh
cp ansible.sample.cfg ansible.cfg
# 修改其中的 `remote_user` 为你在 demo 主机上的用户名
```

### Demo

`inventries/demo` 作为演示用。可以在你的开发机上面配置 `/etc/hosts`，
将域名 `ansible.test` 指向测试用的虚拟机 IP。

### 执行 playbook

```sh
# 测试能否访问到服务器
ansible demo -i inventories/demo/hosts.ini -e "ansible_port=22" \
  -m ping
# 运行 bootstrap playbook
# 如果登录使用的 remote_user 是 root, 则无需提供 -K 选项
ansible-playbook -K -i inventories/demo/hosts.ini playbooks/bootstrap.yml
```

### 使用 ansible-vault

约定所有加密文件为 `.vault` 后缀，加解密时通过 `--vault-id` 指定密码，密码存于 `vaults/`
目录中。假设某个 vault 的密码为 `123456`，其 label 为 `kvko`，则文件 `vaults/kvko`
的内容为 `123456`。使用时由 `--vault-id kvko@vaults/kvko` 指定。

或者将 `--vault-id` 参数值保存于 `ansible.cfg` 中，如此可以省去手动输入 `--vault-id`:

```cfg
vault_identity_list = demo@vaults/demo, demo1@vaults/demo1
```

## playbooks

每个 playbook 由不同的 roles 构成，可为不同场景、特定用途编写相应的 playbook。

[bootstrap]: playbooks/bootstrap.yml

- [x] [`bootstrap.yml`][bootstrap]: 服务器初始化

## roles

[packages]: roles/packages/vars/Debian.yml

- [x] `ohmyzsh`: zsh 配置
- [x] `ssh`: SSH 配置
  - 禁止 root 用户直接登录
  - 禁止密码登录
  - 修改默认端口
- [x] `packages`: 系统软件安装
  - [软件列表][packages]
- [x] `ufw`: 防火墙配置
  - 开放端口：22, 80, 443, 1984, 适用于所有服务器（需在安全组中设置相应规则）
  - 特定服务器的规则在相应的 `host_vars` 中定义

## 开发环境配置

```sh
pip install -r requirements-dev.pip && pre-commit install \
  && pre-commit install --hook-type commit-msg

# 编辑保存密码，所有经过加密的文件约定统一使用 `.vault` 后缀
vim local/vault_password
```

## 常用 ad-hoc 命令

### `ping`

```sh
# e.g.
ansible base -i inventories/demo/hosts.ini -m ping
```

## 参考

- [What is provisioning?](https://www.redhat.com/zh/topics/automation/what-is-provisioning)
- [Introduction to ad-hoc commands](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html)
- [Ansible for DevOps (2nd Edition) by Jeff Geerling](https://www.ansiblefordevops.com/)
- [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html)
