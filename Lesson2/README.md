# 簡単なAnsibleを動かす
## 秘密鍵と公開鍵の用意
```
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/vagrant/.ssh/id_rsa.
Your public key has been saved in /home/vagrant/.ssh/id_rsa.pub. (何も入力せずEnter)
The key fingerprint is: (何も入力せずEnter)
e5:4a:17:14:15:c4:46:33:7f:30:d6:39:3e:09:f6:33 vagrant@sun
The key's randomart image is:
+--[ RSA 2048]----+
|          o*B.+..|
|         .  oB =.|
|          o.. = +|
|         o .   E |
|        S o     +|
|       . o       |
|        .        |
|                 |
|                 |
+-----------------+
```
## 公開鍵追加
```
$ ssh-copy-id localhost
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@localhost's password:(vagrantのパスワードを入力しEnter)

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'localhost'"
and check to make sure that only the key(s) you wanted were added.
```
※これらをしないと、ansibleを使って対象Host(ここではlocalhost)に命令を投げることができない。

## Inventoryを作る
```
$ mkdir inventory
$ vi inventory/inventory.ini
++++編集内容++++
[ansible_master]
localhost
+++++++++++++++
```

## コマンド実行
`$ ansible ansible_master -i inventory/inventory.ini -m setup`
```実行結果
localhost | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "10.0.2.15",
            "10.0.0.10"
        ],
		(中略)
        "ansible_userspace_architecture": "x86_64",
        "ansible_userspace_bits": "64",
        "ansible_virtualization_role": "guest",
        "ansible_virtualization_type": "virtualbox",
        "module_setup": true
    },
    "changed": false
}
```

## Playbookを使ってAnsibleを動かす
`$ vi playbook.yml`
```編集内容
- hosts: ansible_master
  tasks:
     - setup:
```
## playbookを実行
`$ ansible-playbook -i inventory/inventory.ini playbook.yml`
```

PLAY [ansible_master] **********************************************************************************

TASK [Gathering Facts] *******************************************************************************
ok: [localhost]

TASK [setup] *****************************************************************************************
ok: [localhost]

PLAY RECAP *******************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0
```

## Hostsを増やす
`vi inventory/inventory.ini`
```
++++編集内容++++
[ansible_client]
x.x.x.x # 実行対象のIPアドレスを追記
+++++++++++++++
```

## 追加した対象に公開鍵を追加
```
$ ssh-copy-id x.x.x.x
The authenticity of host 'x.x.x.x (x.x.x.x)' can't be established.
ECDSA key fingerprint is 9f:35:e3:db:b4:1a:b3:02:10:02:e4:ca:79:0c:67:d8.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@x.x.x.x's password:(パスワードを入力して、Enter

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'x.x.x.x'"
and check to make sure that only the key(s) you wanted were added.
```

## pingモジュールを実行
```
$ ansible -i inventory/inventory.ini all -m ping
x.x.x.x | SUCCESS => {    # 先程追加した対象に対しても実施されている
    "changed": false,
    "ping": "pong"
}
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
