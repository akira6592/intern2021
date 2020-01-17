# 【ハンズオン1】 Ansible による Web サーバーの構築

Ansible で、Web サーバー構築の作業を自動化するための準備をします。

- 自動化する処理の内容
  - httpd のインストール
  - 設定変更
  - コンテンツの配置
  - サービスの起動

## 1.1. 作業ディレクトリへの移動

- `~/intern2021/handson1` ディレクトリに移動します。
```
cd ~/intern2021/handson1
```

- `ls` コマンドで、以下の3つのファイルがあることを確認します。
  - `index.html`: Web サーバーにデプロイするコンテンツ
  - `inventory.ini`: インベントリ （要編集）
  - `web.yml`: Playbook （要編集）

## 1.2. インベントリの作成
- `vi` などのエディタで、インベントリファイル「`inventory.ini`」 の `XXX` の箇所を編集し、以下のように完成させます。

```
[web]
node1
```

## 1.3. Playbook の作成
- `vi` などのエディタで、Playbook「`web.yml`」の `XXX` の箇所を編集し、以下のように完成させます。


```yaml
---
- hosts: web
  become: yes
  gather_facts: no

  tasks:
    - name: install httpd
      yum:
        name: httpd
        state: latest

    - name: deploy index.html
      copy:
        src: index.html
        dest: /var/www/html/index.html

    - name: configure Listen Port
      lineinfile:
        path: /etc/httpd/conf/httpd.conf
        regexp: '^Listen 80$'
        line: 'Listen 8080'
        validate: httpd -tf %s
      notify:
        - restart httpd
        
  handlers:
    - name: restart httpd
      service:
        name: httpd
        state: restarted
        enabled: yes
```

以上で、Ansible による自動化の準備は完了です。

## 参考: 利用しているモジュールのドキュメント
- [yum – Manages packages with the yum package manager](https://docs.ansible.com/ansible/latest/modules/yum_module.html)
- [lineinfile – Manage lines in text files](https://docs.ansible.com/ansible/latest/modules/lineinfile_module.html)
- [copy – Copy files to remote locations](https://docs.ansible.com/ansible/latest/modules/copy_module.html)
- [service – Manage services](https://docs.ansible.com/ansible/latest/modules/service_module.html)