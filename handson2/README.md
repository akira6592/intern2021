# 【ハンズオン2】 Ansible による ユーザーの追加

Ansible で、ユーザーの追加作業を自動化します。本ハンズオンは、このドキュメントが Playbook の準備と手順書を兼ねています。

- 自動化する処理の内容
  - ユーザーの作成
  - パスワードの設定

## 2.1. Ansible 環境の有効化と作業ディレクトリへの移動

- Ansible 環境を有効化します。(すでに有効化済みの場合は省略化)
```
source /opt/ansible/bin/activate
```

- プロンプトが以下のように `(ansible)` から始まるように変わったことを確認します。
```
(ansible) [ansible@controller ~]$ 
```

- `~/intern2021/handson2` ディレクトリに移動します。
```
cd ~/intern2021/handson2
```

- `ls` コマンドで、以下のファイルがあることを確認します。
  - `inventory.ini`: インベントリ
  - `user.yml`: Playbook （要編集）

## 2.2. Playbook の作成
- `vi` などのエディタで、Playbook「`user.yml`」の `XXX` の箇所を編集し、以下のように完成させます。ここでは、パスワードをユーザー名と同じものを設定します。

```yaml
---
- hosts: web
  become: yes
  gather_facts: no
  
  vars_prompt:
    name: password
    prompt: Inupt password for new users

  tasks:
    - name: add users
      user:
        name: "{{ item }}"
        password: "{{ password | password_hash('sha512') }}"
      loop:
        - user01
        - user02
        - user03
```

## 2.3. Playbook の実行
- `ansible-playbook` コマンドで Playbook を実行します。`Inupt passoword for new users:` という表示で止まるので、追加ユーザーに設定したパスワードを入力してエンターを押します。すべてのタスクが「ok」または「changed」で完了することを確認します。

```
ansible-playbook -i inventory.ini user.yml 
```

実行ログ例
```
(ansible) [ansible@controller answer]$ ansible-playbook -i inventory.ini user.yml 
Inupt passoword for new users:  (追加ユーザーに設定したパスワードを入力してエンター)

PLAY [web] ******************************************************************************************

TASK [add users] ************************************************************************************
changed: [web01] => (item=user01)
changed: [web01] => (item=user02)
changed: [web01] => (item=user03)

PLAY RECAP *****************************************************************************************
node1        : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
## 2.4. 事後確認

- `node1` に、ユーザー `user01` でログインできることを確認し、ログアウトする。
```
$ ssh user01@node1
user01@node1's password:  (user01のパスワードを入力してエンター)
[user01@node1 ~]$ hostname  # ホスト名確認
node1
[user01@node1 ~]$ exit      # ログアウト
$
```

## 参考: 利用しているモジュールのドキュメント
- [user – Manage user accounts](https://docs.ansible.com/ansible/latest/modules/user_module.html)
