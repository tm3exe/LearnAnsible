# Ansibleのインストール
## 関連パッケージをインストール
```
$ sudo apt-get install software-properties-common
```
## 関連レポジトリの追加
```
$ sudo apt-add-repository ppa:ansible/ansible
```
## パッケージの更新
```
$ sudo apt-get update
```
## Ansibleのインストール
```
$ sudo apt-get install ansible
```
## Ansibleの動作確認
```
$ ansible --version
```
## Ansibleでlocalhostへのping確認
```
$ ansible -u vagrant localhost -m ping --ask-pass
```
