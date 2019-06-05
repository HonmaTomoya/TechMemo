# 準備するもの
  - terminal
  - git
  - virtualbox
  - vagrant

# Homesteadとは
  Laravelの実行に必要な基本構成(PHP、nginx、webサーバ、MySQL)のほか、Redisやmemocachedなど
  のミドルウェア、簡易メールサーバのMailhogなどがセットになった環境。

# vagrant環境の作成
## Homestead boxのインストール
```
cd ~/
vagrant box add laravel/homestead
```

## Homestead設定ファイルのダウンロード
```
git clone https://github.com/laravel/homestead.git Homestead
```

## Homesteadの初期化
```
cd ~/Homestead # 設定ファイルのディレクトリへ移動
bash init.sh
```

## Homesteadの設定
```
vim Homestead.yaml
```

```
# ~/Homested/Homestead.yaml
---
ip: "192.168.10.10"
memory: 2048
cpus: 2
provider: virtualbox

authorize: ~/.ssh/TomoyaHonma-GitHub.pub

keys:
    - ~/.ssh/TomoyaHonma-GitHub

folders:
    - map: ~/projects
      to: /home/vagrant/code

sites:
    - map: homestead.test
      to: /home/vagrant/code/Laravel/public

databases:
    - homestead

# ports:
#     - send: 50000
#       to: 5000
#     - send: 7777
#       to: 777
#       protocol: udp
```
  1. ssh鍵を設定する
  1. folders、sitesの設定を任意のパスに変更する
    - folders: ローカルマシン側のプロジェクト配置パス
    - sites: 仮想マシン側のプロジェクト配置パス
  また、設定ファイル変更の反映は、下記コマンドを使用
  ```
  vagrant up --provision
  ```
## 仮想マシンを起動
```
# ~/Homestead
vagrant up
```
## 仮想マシンを接続
```
# ~/Homestead
vagrant ssh
```

## Laravelプロジェクトを作成
```
(VM) ~/
cd code
laravel new # 最新版の作成
composer create-project --prefer-dist laravel/laravel "5.5.*" # バージョンを指定
```

## ページにアクセス
  1. `homestead.yml` で設定したURLにアクセスする(デフォルト: 192.168.10.10)

  2. `no input file specified` と表示される場合は、
  `homestead.yml` で設定した、 `folders` と `sites` が間違っている。
  Laravelの場合は、 `public/index.php` へのパスを指定すれば良いので、改めて確認する。

## IPアドレスに名前をつける

  1. `homestead.yml` で名前を決定する
  ```
sites:
    - map: homestead.test
      to: /home/vagrant/code/Laravel/public
  ```
  1. ローカルマシンで名前解決させる
  ```
sudo vim /etc/hosts

##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1       localhost
192.168.10.10   homestead.test
255.255.255.255 broadcasthost
::1             localhost
  ```
  1. `homestead.test` でアクセスする
