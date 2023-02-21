## lesson42 rsync

```shell
$ rsync -v -e 'ssh -i udemy-github-actions-key.pem' backend/dist/index.js ubuntu@ubuntu@xxxxxxxxxxxx.ap-northeast-1.compute.amazonaws.com:~/index.js
```

## lessson43

EC2 に node をインストールする。
https://github.com/nodesource/distributions

```shell
$ curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash - &&\
$ sudo apt-get install -y nodejs
$ node -v
v16.19.1
```

## lesson44

EC2 上で実行してみる。

```shell
$ node index.js

$ sudo PORT=80 node index.js
```

## lesson45

```shell
# backgroundで実行。
$ sudo PORT=80 node index.js &
# この状態のとき、アプリが動いているのかを確認する。
$ ssh -i "udemy-github-actions-key.pem" ubuntu@ec2-xx-xxx-xx-xx.ap-northeast-1.compute.amazonaws.com
# プロセスを確認する
$ ps aux
$ ps aux | grep "node index.js"
# 使用しているポートを確認する
$ sudo ss -antup
```

## lesson46

```shell
# 木構造でプロセスを確認
$ pstree -p
# systemdに実行してもらうのが一番いい
```

## lesson47

systemd に実行してもらうのが一番いい。サーバー再起動などで便利

```shell
$ ls /etc/systemd/system/
# このディレクトリに設定ファイルを置けばOK
# ただ、自分で作るのはちょっと面倒くさい。
# https://expressjs.com/ja/advanced/pm.html#systemd
# を使用したらよい

$ sudo vi /etc/systemd/system/backend.service
$ cat /etc/systemd/system/backend.service
# systemdを再起動
$ sudo systemctl daemon-reload
$ systemctl status backend
$ sudo systemctl start backend
$ systemctl status backend

# 今回lesson47のままだと、エラーになるはず。
× backend.service - Express
     Loaded: loaded (/etc/systemd/system/backend.service; disabled; vendor preset: enabled)
     Active: failed (Result: exit-code) since Mon 2023-02-20 13:31:52 UTC; 5s ago
    Process: 2723 ExecStart=/usr/bin/node index.js (code=exited, status=1/FAILURE)
   Main PID: 2723 (code=exited, status=1/FAILURE)
        CPU: 120ms

Feb 20 13:31:52 ip-172-31-11-111 systemd[1]: backend.service: Main process exited, code=exited, status=1/FAILURE
Feb 20 13:31:52 ip-172-31-11-111 systemd[1]: backend.service: Failed with result 'exit-code'.
Feb 20 13:31:52 ip-172-31-11-111 systemd[1]: backend.service: Scheduled restart job, restart counter is at 5.
Feb 20 13:31:52 ip-172-31-11-111 systemd[1]: Stopped Express.
Feb 20 13:31:52 ip-172-31-11-111 systemd[1]: backend.service: Start request repeated too quickly.
Feb 20 13:31:52 ip-172-31-11-111 systemd[1]: backend.service: Failed with result 'exit-code'.
Feb 20 13:31:52 ip-172-31-11-111 systemd[1]: Failed to start Express.
```

## lesson48 ログの確認とエラーの解決

```shell
$ journalctl -u backend
# 以下の個所に着目
Feb 20 13:31:52 ip-172-31-11-111 node[2723]: node:events:491
Feb 20 13:31:52 ip-172-31-11-111 node[2723]:       throw er; // Unhandled 'error' event
Feb 20 13:31:52 ip-172-31-11-111 node[2723]:       ^
Feb 20 13:31:52 ip-172-31-11-111 node[2723]: Error: listen EADDRINUSE: address already in use :::80
Feb 20 13:31:52 ip-172-31-11-111 node[2723]:     at Server.setupListenHandle [as _listen2] (node:net:1463:16)

$ sudo ss -antup
tcp           LISTEN         0              511                                     *:80                                  *:*              users:(("node",pid=2390,fd=20))
$ ps aux
root        2390  0.0  4.2 626440 42456 pts/1    Sl   13:12   0:00 node index.js
# 一番最初に手作業で実行したnodejsが動いていた
# なので、killする
$ sudo kill 2390
# ちゃんとプロセスが消えたか確認する
$ ps aux
$ sudo ss -antup
```

## lesson49 systemd によるアプリケーションの実行

前回余計なプロセスは kill したので、systemd で実行できるはず

```shell
$ sudo systemctl start backend
$ systemctl status backend

# 再起動しても、backend.serviceが実行されるようにする。
$ sudo systemctl enable backend
# すべてのユニットファイルの一覧と自動起動の状態を確認する。
$ systemctl list-unit-files --type=service
```

# 参考

- [systemctl の起動中のサービス一覧を表示するコマンド](https://www.suzu6.net/posts/303-systemctl-list)
