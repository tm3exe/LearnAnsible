# Webサーバを構築
## Inventoryの更新
`$ vi inventory/inventory.ini`
```
[webserver]
(WebサーバにするIPアドレス)
```
ここではLesson2で使ったx.x.x.xを用いる

※新しい対象のときはssh-copy-id (WebサーバにするIPアドレス)を忘れずに。

## setupモジュールが実行できるかテスト実行
`$ ansible -i inventory/inventory.ini webserver -m setup`
```
ログは略 (Lesson2参照)
```

## Apacheインストールを行うYAMLを書く
`$ vi web.yml`
```
---
- hosts: webserver
  sudo: yes
  tasks:
   - name: 基本パッケージをインストール
     apt: name={{item}} state=present update_cache=yes
     with_items:
         - git
         - vim
         - ntp
         - language-pack-ja

   - name: aptでApacheインストール
     apt:
       name=apache2
       state=present

   - name: Apacheの起動とchkconfig登録
     service:
       name=apache2
       state=started
       enabled=yes

   - name: index.htmlを作成
     shell: echo '<p1>It Works.</p1>' > /var/www/html/index.html
```

## Apacheインストール on ansible
`$ ansible-playbook web.yml -i inventory/inventory.ini
`
```
[DEPRECATION WARNING]: Instead of sudo/sudo_user, use become/become_user and make sure become_method
is 'sudo' (default).
This feature will be removed in a future release. Deprecation warnings can be
disabled by setting deprecation_warnings=False in ansible.cfg.

PLAY [webserver] *************************************************************************************

TASK [Gathering Facts] *******************************************************************************
ok: [x.x.x.x]

TASK [基本パッケージをインストール] ********************************************************************************
ok: [x.x.x.x] => (item=[u'git', u'vim', u'ntp', u'language-pack-ja'])

TASK [aptでApacheインストール] ******************************************************************************
ok: [x.x.x.x]

TASK [Apacheの起動とchkconfig登録] *************************************************************************
ok: [x.x.x.x]

TASK [index.htmlを作成] *********************************************************************************
changed: [x.x.x.x]

PLAY RECAP *******************************************************************************************
x.x.x.x                  : ok=5    changed=1    unreachable=0    failed=0
```
## Apacheが動いているか、確認
CLI
```
$ curl x.x.x.x
<p1>It Works.</p1>
```
GUI
ブラウザでWebサーバのIPアドレスを入力して、アクセスする。
It Worksと表示されればOK

## 冪等性を確かめる
`$ ansible-playbook web.yml -i inventory/inventory.ini`
上記のコマンドを複数回実行しても、正常完了する。
